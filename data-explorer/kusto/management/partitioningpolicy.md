---
title: 데이터 분할 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책 및이 정책을 사용 하 여 쿼리 성능을 개선 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: fdf72c8c58ec8a9fb9c64ecea6219dcc3a736d18
ms.sourcegitcommit: 2bdb904e6253c9ceb8f1eaa2da35fcf27e13a2cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97091354"
---
# <a name="partitioning-policy"></a>분할 정책

분할 정책은 특정 테이블에 대해 [익스텐트 (데이터 분할)](../management/extents-overview.md) 를 분할 해야 하는지 여부 및 방법을 정의 합니다.

정책의 주요 목적은 데이터 집합의 범위를 좁히는 쿼리 성능을 향상 시키는 것입니다. 예를 들어 분할 된 열에 대해 필터링 하거나 집계를 사용 하거나 높은 카디널리티 문자열 열에 조인 하는 쿼리입니다. 이 정책으로 인해 데이터 압축이 향상 될 수도 있습니다.

> [!CAUTION]
> 분할 정책이 정의 된 테이블 수에는 하드 코드 된 한도가 설정 되지 않습니다. 그러나 모든 추가 테이블은 클러스터의 노드에서 실행 되는 백그라운드 데이터 분할 프로세스에 오버 헤드를 추가 합니다. 테이블을 추가 하면 더 많은 클러스터 리소스가 사용 될 수 있습니다. 자세한 내용은 [모니터링](#monitor-partitioning) 및 [용량](#partition-capacity)을 참조 하세요.

## <a name="partition-keys"></a>파티션 키

지원 되는 파티션 키 종류는 다음과 같습니다.

|Kind                                                   |열 유형 |파티션 속성                                               |파티션 값                                        |
|-------------------------------------------------------|------------|-------------------------------------------------------------------|-------------------------------------------------------|
|[해시](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed`, `PartitionAssignmentMode` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[균일 범위](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`, `OverrideCreationTime`                   | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>해시 파티션 키

> [!NOTE]
> `string`다음 경우에만 테이블의 형식 열에 해시 파티션 키를 적용 합니다.
> * 대부분의 쿼리에서 같음 필터 ( `==` ,)를 사용 하는 경우 `in()`
> * 대다수의 쿼리는 `string` ,, 또는와 같은 *큰 차원의* 특정 열 (10M 이상의 카디널리티)에 대해 집계/조인 합니다 `application_ID` `tenant_ID` `user_ID` .

* 해시 모듈로 함수는 데이터를 분할 하는 데 사용 됩니다.
* 유형이 같은 (분할 된) 익스텐트의 데이터는 해시 파티션 키를 기준으로 정렬 됩니다.
  * 테이블에 해시 파티션 키가 정의 되어 있는 경우에는 해당 키를 [행 순서 정책](roworderpolicy.md)에 포함할 필요가 없습니다.
* [순서 섞기 전략](../query/shufflequery.md)을 사용 하 고 `shuffle key` 에서 사용 되는 쿼리는 `join` `summarize` `make-series` 클러스터 노드 간에 이동 하는 데 필요한 데이터 양이 줄어들기 때문에 더 나은 성능을 기대할 수 있습니다.

#### <a name="partition-properties"></a>파티션 속성

|속성 | Description | 지원 되는 값| 권장되는 값 |
|---|---|---|---|
| `Function` | 사용할 hash 모듈로 함수의 이름입니다.| `XxHash64` | |
| `MaxPartitionCount` | 기간을 만들 최대 파티션 수 (해시 모듈로 함수에 대 한 모듈로 인수)입니다. | 범위에 `(1,2048]` 있습니다. <br>  클러스터에 있는 노드 수의 5 배 보다 크고 열의 카디널리티 보다 작습니다. |  값이 높을수록 클러스터의 노드에서 데이터 분할 프로세스의 오버 헤드가 증가 하 고, 각 기간에 대 한 익스텐트 수가 커집니다. 50 노드 미만의 클러스터의 경우부터 시작 `256` 합니다. 이러한 고려 사항에 따라 또는 쿼리 성능 및 데이터 사후 수집을 분할 하는 오버 헤드의 장점에 따라 값을 조정 합니다.
| `Seed` | 해시 값을 임의로 선택할 하는 데 사용 합니다. | 양의 정수 | `1`이며 기본값 이기도 합니다. |
| `PartitionAssignmentMode` | 클러스터의 노드에 파티션을 할당 하는 데 사용 되는 모드입니다. | `Default`: 같은 파티션에 속하는 모든 동일 (분할 된) 익스텐트가 동일한 노드에 할당 됩니다. <br> `Uniform`: 익스텐트의 파티션 값은 무시 됩니다. 익스텐트는 클러스터의 노드에 균일 하 게 할당 됩니다. | 쿼리가 해시 파티션 키에서 조인 또는 집계 되지 않는 경우를 사용 `Uniform` 합니다. 그렇지 않으면 `Default`를 사용합니다. |

#### <a name="hash-partition-key-example"></a>해시 파티션 키 예제

이라는 형식화 된 열에 대 한 해시 파티션 키 `string` `tenant_id` 입니다.
이 함수는 `XxHash64` 해시 함수를 사용 하며,의는와 기본값을 사용 `MaxPartitionCount` `256` `Seed` `1` 합니다.

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1,
    "PartitionAssignmentMode": "Default"
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>균일 범위 datetime 파티션 키

> [!NOTE] 
> `datetime`테이블의 데이터 수집이 열에 따라 정렬 되지 않을 경우 테이블의 형식화 된 열에 대해 균일 한 범위 datetime 파티션 키만 적용 합니다.

이러한 경우 익스텐트 간에 데이터를 재조정 수 있으므로 각 익스텐트는 제한 된 시간 범위의 레코드를 포함할 수 있습니다. 이렇게 하면 열에 대 한 필터가 `datetime` 쿼리 시 보다 효과적으로 처리 됩니다.

사용 된 파티션 함수는 [bin_at ()](../query/binatfunction.md) 되며 사용자 지정할 수 없습니다.

#### <a name="partition-properties"></a>파티션 속성

|속성                | Description                                                                                                                                                     | 권장되는 값                                                                                                                                                                                                                                                            |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `RangeSize`            | `timespan`각 datetime 파티션의 크기를 나타내는 스칼라 상수입니다.                                                                                | 값으로 시작 `1.00:00:00` 합니다 (1 일). 더 짧은 값을 설정 하지 마세요 .이로 인해 병합할 수 없는 작은 익스텐트가 여러 개 있을 수 있습니다.                                                                                                      |
| `Reference`            | `datetime`정렬 된 datetime 파티션에 따라 고정 된 특정 시점을 나타내는 스칼라 상수입니다.                                          | `1970-01-01 00:00:00`를 시작합니다. Datetime 파티션 키에 값이 있는 레코드가 있는 경우 `null` 해당 파티션 값은의 값으로 설정 됩니다 `Reference` .                                                                                                      |
| `OverrideCreationTime` | `bool`결과 익스텐트의 최소 및 최대 생성 시간이 파티션 키의 값 범위에 의해 재정의 되어야 하는지 여부를 나타내는입니다. | 기본값은 `false`입니다. `true`데이터가 도착 시 수집 되지 않은 경우 (예: 단일 소스 파일에 매우 멀리 있는 datetime 값이 포함 될 수 있는 경우)로 설정 하 고, 수집 시간이 아닌 날짜/시간 값을 기반으로 보존/캐싱을 강제 적용 하려는 경우로 설정 합니다. |

#### <a name="uniform-range-datetime-partition-example"></a>단일 범위 datetime 파티션 예

이 코드 조각은 이라는 형식화 된 열에 대해 균일 한 datetime 범위 파티션 키를 보여 줍니다 `datetime` `timestamp` .
이 클래스는를 `datetime(1970-01-01)` 참조 지점으로 사용 하 고 각 파티션에는 크기를 지정 `1d` 하며 익스텐트의 생성 시간은 재정의 하지 않습니다.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00",
    "OverrideCreationTime": false
  }
}
```

## <a name="the-policy-object"></a>정책 개체

기본적으로 테이블의 데이터 분할 정책은입니다 .이 `null` 경우 테이블의 데이터는 분할 되지 않습니다.

데이터 분할 정책에는 다음과 같은 주요 속성이 있습니다.

* **파티션 키**:
  * 테이블의 데이터를 분할 하는 방법을 정의 하는 [파티션 키](#partition-keys) 의 컬렉션입니다.
  * 테이블에는 다음 옵션 중 하나를 사용 하 여 최대 파티션 키를 사용할 수 있습니다 `2` .
    * [해시 파티션 키](#hash-partition-key)1 개
    * 단일 [범위 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
    * 하나의 [해시 파티션 키](#hash-partition-key) 와 하나의 [단일 범위 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
  * 각 파티션 키에는 다음과 같은 속성이 있습니다.
    * `ColumnName`: `string ` -분할 되는 데이터에 따라 열의 이름입니다.
    * `Kind`: `string` 적용할 데이터 분할 종류 ( `Hash` 또는 `UniformRange` )입니다.
    * `Properties`: `property bag` -분할에 따라 수행 되는 매개 변수를 정의 합니다.

* **EffectiveDateTime**:
  * 정책이 적용 되는 UTC 날짜/시간입니다.
  * 이 속성은 선택 사항입니다. 지정 하지 않으면 정책이 적용 된 후에 정책이 데이터 수집 적용 됩니다.
  * 보존으로 인해 삭제 될 수 있는 유형이 아닌 (분할 되지 않은) 익스텐트는 모두 분할 프로세스에서 무시 됩니다. 범위는 생성 시간이 테이블의 효과적인 일시 삭제 기간의 90% 보다 이전 이므로 무시 됩니다.
    > [!NOTE]
    > 과거에는 datetime 값을 설정 하 고 이미 수집 데이터를 분할할 수 있습니다. 그러나이 방법을 사용 하면 분할 프로세스에 사용 되는 리소스가 상당히 늘어날 수 있습니다.
    > 정책을 변경할 때마다 최대 며칠의 이전 단계에서 *EffectiveDateTime* 을 이전으로 설정 하 여 점차적으로 수행 하는 것이 좋습니다 `datetime` .

### <a name="data-partitioning-example"></a>데이터 분할 예

두 개의 파티션 키가 있는 데이터 분할 정책 개체입니다.
1. 이라는 형식화 된 열에 대 한 해시 파티션 키 `string` `tenant_id` 입니다.
    * 이 함수는 `XxHash64` 해시 함수를 사용 하며 `MaxPartitionCount` ,의 기본값은 256입니다 `Seed` `1` .
1. 라는 유형 열에 대 한 균일 한 datetime 범위 파티션 키 `datetime` `timestamp` 입니다.
    * `datetime(1970-01-01)`각 파티션에 대해 크기를 사용 하 여를 참조 지점으로 사용 `1d` 합니다.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1,
        "PartitionAssignmentMode": "Default"
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}
```

## <a name="additional-properties"></a>추가 속성

다음 속성은 정책의 일부로 정의 될 수 있습니다. 이러한 속성은 선택 사항이 며 변경 하지 않는 것이 좋습니다.

|속성 | Description | 권장되는 값 | 기본값 |
|---|---|---|---|
| **MinRowCountPerOperation** |  단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최소 대상입니다. | | `0` |
| **MaxRowCountPerOperation** |  단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최대 대상입니다. | 분할 작업에서 작업 당 많은 양의 메모리 또는 CPU를 사용 하는 경우 5M 보다 작은 값을 설정 합니다. 자세한 내용은 [monitoring](#monitor-partitioning)을 참조 하세요. | `0`이며 기본 대상은 500만 레코드입니다. |

## <a name="the-data-partitioning-process"></a>데이터 분할 프로세스

* 데이터 분할은 클러스터에서 사후 수집 백그라운드 프로세스로 실행 됩니다.
  * 지속적으로 수집 되는 테이블은 항상 분할 (유형이 다른 익스텐트) 할 수 있는 데이터의 "비상"이 있어야 합니다.
* 데이터 분할은 정책의 속성 값에 관계 없이 핫 익스텐트 에서만 실행 됩니다 `EffectiveDateTime` .
  * 콜드 익스텐트가 분할 되어야 하는 경우 [캐싱 정책을](cachepolicy.md)일시적으로 조정 해야 합니다.

## <a name="monitor-partitioning"></a>분할 모니터링

명령을 사용 [`.show diagnostics`](../management/diagnostics.md#show-diagnostics) 하 여 클러스터에서의 분할 상태를 모니터링 합니다.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

출력에는 다음이 포함 됩니다.

  * `MinPartitioningPercentageInSingleTable`: 클러스터에 데이터 분할 정책이 있는 모든 테이블에서 분할 된 데이터의 최소 비율입니다.
    * 이 백분율이 지속적으로 90% 미만으로 유지 되 면 클러스터의 파티션 [용량](partitioningpolicy.md#partition-capacity)을 평가 합니다.
  * `TableWithMinPartitioningPercentage`: 분할 백분율이 위에 표시 된 테이블의 정규화 된 이름입니다.

[`.show commands`](commands.md)분할 명령 및 해당 리소스 사용을 모니터링 하는 데 사용 합니다. 예를 들어:

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

## <a name="partition-capacity"></a>파티션 용량

* 데이터 분할 프로세스로 인해 더 많은 익스텐트가 생성 됩니다. 클러스터는 익스텐트 [병합 용량](../management/capacitypolicy.md#extents-merge-capacity)을 점진적으로 늘릴 수 있으므로 [익스텐트](../management/extents-overview.md) 병합 프로세스가 계속 될 수 있습니다.
* 수집 처리량이 많고 분할 정책이 정의 된 테이블이 충분히 많은 경우 클러스터는 범위 [파티션 용량](../management/capacitypolicy.md#extents-partition-capacity)을 점차적으로 늘릴 수 있으므로 [분할 익스텐트의 프로세스가](#the-data-partitioning-process) 계속 될 수 있습니다.
* 리소스를 너무 많이 사용 하지 않도록 이러한 동적 증가가 더 이상 사용 되지 않습니다. 전체적으로 사용 되는 경우에는 캡을 넘어 점차 증가 하는 것이 필요할 수 있습니다.
  * 용량을 [늘리면 클러스터의](../../manage-cluster-vertical-scaling.md)리소스 사용이 크게 증가 하는 경우 / [](../../manage-cluster-horizontal-scaling.md)수동으로 또는 자동 크기 조정을 사용 하 여 클러스터를 확장할 수 있습니다.

## <a name="outliers-in-partitioned-columns"></a>분할 된 열의 이상 값

* 다음 상황에서는 클러스터의 노드 간에 분산 된 데이터 분산에 영향을 주어 쿼리 성능을 저하 시킬 수 있습니다.
    * 해시 파티션 키에 다른 값 (예: 빈 문자열) 또는 제네릭 값 (예: `null` 또는) 보다 훨씬 더 많은 값이 포함 된 경우 `N/A`
    * 값은 `tenant_id` 데이터 집합에서 더 자주 발생 하는 엔터티 (예:)를 나타냅니다.
* 균일 범위의 datetime 파티션 키에 열에 있는 대부분의 값에서 "far"로 충분 한 비율의 값이 있는 경우 데이터 분할 프로세스의 오버 헤드가 증가 하 고 클러스터에서 추적을 유지 해야 하는 작은 익스텐트가 많이 발생할 수 있습니다. 이러한 상황의 예로는 과거 또는 미래의 날짜/시간 값이 있습니다.

두 경우 모두 데이터를 "수정" 하거나 수집 시간 전후에 데이터에 있는 관련이 없는 레코드를 필터링 하 여 클러스터에서 데이터 분할의 오버 헤드를 줄입니다. 예를 들어 [업데이트 정책을](updatepolicy.md)사용 합니다.

## <a name="next-steps"></a>다음 단계

[분할 정책 제어 명령을](../management/partitioning-policy.md) 사용 하 여 테이블에 대 한 데이터 분할 정책을 관리할 수 있습니다.
