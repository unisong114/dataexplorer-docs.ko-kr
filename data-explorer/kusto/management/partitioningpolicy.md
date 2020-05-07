---
title: 데이터 분할 정책 (미리 보기)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책 (미리 보기)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 6ce7cf38c88879b52c4e2e259e3e9a5cade959de
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907149"
---
# <a name="data-partitioning-policy-preview"></a>데이터 분할 정책 (미리 보기)

분할 정책은 특정 테이블에 대해 [익스텐트 (데이터 분할)](../management/extents-overview.md) 가 분할 되어야 하는지 여부 및 방법을 정의 합니다.

> [!NOTE]
> 데이터 분할 기능은 *미리 보기*상태입니다.

정책의 주된 목적은 분할 된 열에 있는 값의 작은 하위 집합으로 축소 되는 것으로 알려진 쿼리 성능 및 높은 카디널리티 문자열 열에 대 한 집계/조인을 개선 하는 것입니다. 보조 잠재적인 혜택은 데이터 압축을 향상 시키는 것입니다.

> [!WARNING]
> 정책을 정의할 수 있는 테이블의 양에는 하드 코드 된 제한이 없지만 모든 추가 테이블은 클러스터의 노드에서 실행 되는 백그라운드 데이터 분할 프로세스에 오버 헤드를 추가 하 고 클러스터의 추가 리소스가 필요할 수 있습니다. [용량](#capacity)을 참조 하세요.

## <a name="partition-keys"></a>파티션 키

지원 되는 파티션 키 종류는 다음과 같습니다.

|종류                                                   |열 유형 |파티션 속성                    |파티션 값                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[해시](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[균일 범위](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>해시 파티션 키

테이블의 형식화 된 열에 `string`해시 파티션 키를 적용 하는 것은 *대부분* 의 쿼리에서 *큰 차원* (10M 이상의 카디널리티`==`) `in()` `string` `application_ID` `tenant_ID` 의 특정 형식 열에 대해 같음 필터 (,) 및/또는 집계/조인을 사용 하는 경우에 적합 합니다 `user_ID`.

* 해시 모듈로 함수는 데이터를 분할 하는 데 사용 됩니다.
* 동일한 파티션에 *속하는 모든 동일* (분할 된) 익스텐트는 동일한 데이터 노드에 할당 됩니다.
* *유형이* 같은 (분할 된) 익스텐트의 데이터는 해시 파티션 키를 기준으로 정렬 됩니다.
  * 테이블에 정의 된 경우 [행 순서 정책](roworderpolicy.md)에 파티션 키를 포함 하는 것은 필요 하지 않습니다.
* [순서 섞기 전략](../query/shufflequery.md)을 사용 `shuffle key` 하 고에서 `join` `summarize` `make-series` 사용 되는 쿼리는 클러스터 노드 간에 이동 하는 데 필요한 데이터 양이 크게 감소 하기 때문에 더 나은 성능을 기대할 수 있습니다.

#### <a name="partition-properties"></a>파티션 속성

* `Function`사용할 hash 모듈로 함수의 이름입니다.
  * 지원 되는 `XxHash64`값은입니다.
* `MaxPartitionCount`만들 파티션의 최대 수 (해시 모듈로 함수에 대 한 모듈로 인수)입니다.
  * 지원 되는 값은 범위 `(1,1024]`에 있습니다.
    * 값은 다음과 같아야 합니다.
      * 클러스터의 노드 수보다 큼
      * 열 카디널리티 보다 작습니다.
    * 값이 높을수록 클러스터 노드의 데이터 분할 프로세스에 대 한 오버 헤드가 증가 하 고, 각 기간의 익스텐트 수가 늘어납니다 (단위).
    * 시작 하는 데 권장 되는 `256`값은입니다.
      * 필요한 경우 앞에서 언급 한 고려 사항에 따라 조정 하거나, 쿼리 성능 및/또는 데이터 사후 수집의 분할 오버 헤드의 이점을 측정 하는 방법에 따라 조정 될 수 있습니다.
* `Seed`해시 값을 임의로 선택할 하는 데 사용할 값입니다.
  * 값은 양의 정수 여야 합니다.
  * 권장 (지정 하지 않은 경우 기본값) 값은 `1`입니다.

#### <a name="example"></a>예제

다음은 라는 `string` `tenant_id`형식화 된 열에 대 한 해시 파티션 키입니다.

이 함수는 `XxHash64` 해시 함수를 사용 하며 `MaxPartitionCount` , `256`의는와 기본값 `Seed` 을 `1`사용 합니다.

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>균일 범위 datetime 파티션 키

테이블에서 형식화 된 열에 `datetime`균일 한 범위 datetime 파티션 키를 적용 하는 것은 테이블의 데이터 수집이 열에 따라 정렬 되지 않는 경우에 적합 합니다. *unlikely* 이러한 경우 제한 된 시간 범위의 레코드를 포함 하 여 각 익스텐트가 종료 되도록 익스텐트 간에 데이터 순서를 다시 조정 하는 것이 도움이 될 수 있습니다. 그러면 쿼리 시간에 `datetime` 열에 대 한 필터가 더 효율적으로 생성 됩니다.

* 사용 된 파티션 함수는 [bin_at ()](../query/binatfunction.md) 되며 사용자 지정할 수 없습니다.

#### <a name="partition-properties"></a>파티션 속성

* `RangeSize`각 datetime `timespan` 파티션의 크기를 나타내는 스칼라 상수입니다.
* `Reference`정렬 된 `datetime` datetime 파티션에 따라 고정 된 특정 시점을 나타내는 형식의 스칼라 상수입니다.
  * Datetime 파티션 키에 값이 `null` 있는 레코드가 있는 경우 해당 파티션 값은의 `Reference`값으로 설정 됩니다.

#### <a name="example"></a>예제

다음은 라는 형식화 된 열에 `datetime`대 한 균일 한 datetime 범위 파티션 키입니다.`timestamp`

각 파티션에 `datetime(1970-01-01)` `1d` 대해 크기를 사용 하 여를 참조 지점으로 사용 합니다.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>정책 개체

기본적으로 테이블의 데이터 분할 정책은입니다 .이 `null`경우 테이블의 데이터는 분할 되지 않습니다.

데이터 분할 정책에는 다음과 같은 주요 속성이 있습니다.

* **파티션 키**:
  * 테이블에서 데이터를 분할 하는 방법을 정의 하는 [파티션 키](#partition-keys) 의 컬렉션입니다.
  * 테이블에는 다음 3 가지 `2` 옵션 중 하나를 사용 하 여 최대 파티션 키가 있을 수 있습니다.
    * 1 [해시 파티션 키](#hash-partition-key)입니다.
    * 1 개의 [균일 범위의 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
    * 1 [해시 파티션 키](#hash-partition-key) 및 1 개의 [균일 범위 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
  * 각 파티션 키에는 다음과 같은 속성이 있습니다.
    * `ColumnName`: `string ` -분할 되는 데이터에 따라 열의 이름입니다.
    * `Kind`: `string` 적용할 데이터 분할 종류 (`Hash` 또는 `UniformRange`)입니다.
    * `Properties`: `property bag` -분할에 따라 수행 되는 매개 변수를 정의 합니다.

* **EffectiveDateTime**:
  * 정책이 적용 되는 UTC 날짜/시간입니다.
  * 이 속성은 *선택 사항* 입니다. 지정 하지 않으면 정책이 적용 된 후에 정책이 데이터 수집 적용 됩니다.
  * 보존으로 인해 곧 연결 되는 (즉, 생성 시간은 테이블의 효과적인 일시 삭제 기간의 90% 보다 앞에 옴) 동일 하지 않은 (분할 되지 않은) 익스텐트는 분할 프로세스에서 무시 됩니다.

### <a name="example"></a>예제

다음은 두 개의 파티션 키가 있는 데이터 분할 정책 개체입니다.
1. 이라는 `string` `tenant_id`형식화 된 열에 대 한 해시 파티션 키입니다.
    - 이 함수는 `XxHash64` 해시 함수를 사용 하며 `MaxPartitionCount` ,의 기본값 `Seed` 은 256입니다 `1`.
2. 이라는 형식화 된 열에 `datetime`대 한 균일 한 datetime 범위 파티션 키`timestamp`
    - 각 파티션에 `datetime(1970-01-01)` `1d` 대해 크기를 사용 하 여를 참조 지점으로 사용 합니다.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

### <a name="additional-properties"></a>추가 속성

다음 속성은 정책의 일부로 정의 될 수 있지만 *선택적* 이며 변경 하지 않는 것이 좋습니다.

* **Minrowcountperoperation**:
  * 단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최소 대상입니다.
  * 이 속성은 *선택 사항이 며*기본값은 `0`입니다.

* **Maxrowcountperoperation**:
  * 단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최대 대상입니다.
  * 이 속성은 *선택 사항이 며*기본값 `0` 은입니다 .이 경우에는 500만 레코드의 기본 대상이 적용 됩니다.

## <a name="notes"></a>참고

### <a name="the-data-partitioning-process"></a>데이터 분할 프로세스

* 데이터 분할은 클러스터에서 사후 수집 백그라운드 프로세스로 실행 됩니다.
  * 지속적으로 수집 되는 테이블에는 항상 분할 (*유형이 다른* 익스텐트) 할 데이터의 "tail"이 있어야 합니다.
* 데이터 분할은 정책의 `EffectiveDateTime` 속성 값에 관계 없이 핫 익스텐트 에서만 실행 됩니다.
  * 콜드 익스텐트가 분할 되어야 하는 경우에는 [캐싱 정책을](cachepolicy.md) 일시적으로 조정 해야 합니다.

#### <a name="monitoring"></a>모니터링

* [. 진단 표시](../management/diagnostics.md#show-diagnostics) 명령을 사용 하 여 클러스터에서 분할의 진행률/상태를 모니터링할 수 있습니다.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable,
          TableWithMinPartitioningPercentage
```

출력에는 다음이 포함 됩니다.

  * `MinPartitioningPercentageInSingleTable`: 클러스터에 데이터 분할 정책이 있는 모든 테이블에서 분할 된 데이터의 최소 비율입니다.
      * 이 백분율이 지속적으로 90% 미만으로 유지 되는 경우 클러스터의 파티션 용량 ( [아래](partitioningpolicy.md#capacity)참조)을 평가 합니다.
  * `TableWithMinPartitioningPercentage`: 분할 백분율이 위에 표시 된 테이블의 정규화 된 이름입니다.

#### <a name="capacity"></a>용량

* 데이터 분할 프로세스를 수행 하면 더 많은 익스텐트가 생성 될 수 있으므로 [익스텐트](../management/extents-overview.md) 병합 프로세스를 유지할 수 있도록 (점진적이 고 선형) 클러스터의 [익스텐트 병합 용량](../management/capacitypolicy.md#extents-merge-capacity) 을 늘려야 할 수도 있습니다.
* 필요한 경우 (예를 들어 높은 수집 처리량의 경우 및/또는 분할을 필요로 하는 충분 한 수의 테이블이 필요한 경우) 더 많은 동시 분할 작업을 실행할 수 있도록 클러스터의 [익스텐트 파티션 용량](../management/capacitypolicy.md#extents-partition-capacity) (점진적 및 선형)이 증가할 수 있습니다.
  * 분할을 늘리면 클러스터의 리소스 사용이 크게 증가 하는 경우 수동으로 또는 자동 크기 조정을 사용 하 여 클러스터를 확장/축소할 수 있습니다.

### <a name="outliers-in-partitioned-columns"></a>분할 된 열의 이상 값

* 해시 파티션 키의 값이 정확 하 게 채워지지 않은 (예: 비어 있거나 제네릭 값을 포함 하는) 충분 한 비율의 값을 포함 하는 경우 클러스터의 노드 간에 데이터의 균형이 맞지 않는 분산을 사용할 수 있습니다.
* 균일 범위의 datetime 파티션 키에 열에 있는 대부분의 값 (예: 과거의 날짜/시간 값)에서 "far"로 충분 한 비율의 값이 있는 경우

이러한 두 경우 모두 데이터를 "수정" 하거나 수집 시간 이전 또는 수집 시간 (예: [업데이트 정책](updatepolicy.md)사용)의 모든 관련이 없는 레코드를 필터링 하 여 클러스터에서 데이터 분할의 오버 헤드를 줄여야 합니다.

## <a name="next-steps"></a>다음 단계

[분할 정책 제어 명령을](../management/partitioning-policy.md) 사용 하 여 테이블에 대 한 데이터 분할 정책을 관리할 수 있습니다.
