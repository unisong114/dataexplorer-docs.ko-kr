---
title: 업데이트 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 812a5af932f69d898bb419631fa6ea3c6bc0795e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519400"
---
# <a name="update-policy"></a>정책 업데이트

업데이트 정책은 Kusto가 원본 테이블에 새 데이터가 삽입될 때마다 자동으로 데이터를 부가하도록 지시하는 **대상** **테이블에**설정됩니다. 업데이트 정책의 **쿼리는** 원본 테이블에 삽입된 데이터에서 실행됩니다. 예를 들어 다른 스키마, 보존 정책 등을 통해 한 테이블을 다른 테이블의 필터링된 보기로 만들 수 있습니다.

기본적으로 업데이트 정책을 실행하지 못하면 원본 테이블에 대한 데이터 수집에는 영향을 주지 않습니다. 업데이트 정책이 **트랜잭션으로**정의된 경우 업데이트 정책을 실행하지 못하면 원본 테이블에 데이터를 수집하여 실패하게 됩니다. (업데이트 정책에서 잘못된 쿼리를 정의하는 등의 일부 사용자 오류로 인해 **데이터가** 원본 테이블로 수집되지 않을 수 있으므로 이 작업을 수행할 때 주의를 기울여야 합니다.) 트랜잭션 업데이트 정책의 "경계"에서 수집된 데이터는 단일 트랜잭션에서 쿼리에 사용할 수 있습니다.

업데이트 정책의 쿼리는 원본 테이블에 새로 수집된 데이터만 "볼" 수 있는 특수 모드에서 실행됩니다. 이 쿼리의 일부로 원본 테이블의 이미 수집된 데이터를 쿼리할 수 없습니다. 이렇게 빨리 하면 이차 시간 섭취가 발생합니다.

업데이트 정책은 대상 테이블에 정의되어 있으므로 데이터를 하나의 원본 테이블로 수집하면 해당 데이터에서 여러 쿼리가 실행될 수 있습니다. 업데이트 정책의 실행 순서는 정의되지 않습니다.

예를 들어 원본 테이블이 자유 텍스트 열로 서식이 지정된 흥미로운 데이터가 있는 고속 추적 테이블이라고 가정합니다. 또한 대상 테이블(업데이트 정책이 정의된 경우)은 특정 추적 선만 허용하고 Kusto의 [구문 사용자 지정 연산자를](../query/parseoperator.md)사용하여 원래 자유 텍스트 데이터를 변환하는 잘 구성된 스키마를 사용한다고 가정합니다.

업데이트 정책은 일반 섭취와 유사하게 수행되며 동일한 제한 및 모범 사례가 적용됩니다. 예를 들어 클러스터 의 크기에 맞게 확장되고 대량으로 섭취가 수행되면 보다 효율적으로 작동합니다.

## <a name="commands-that-trigger-the-update-policy"></a>업데이트 정책을 트리거하는 명령

업데이트 정책은 다음 명령 중 한 가지를 사용하여 데이터를 수집하거나 테이블로 이동할 때 적용됩니다.

* [[1]](../management/data-ingestion/ingest-from-storage.md)
* [.ingest(인라인)](../management/data-ingestion/ingest-inline.md)
* [.set | .부속 | .set-or-append | .set-or-replace](../management/data-ingestion/ingest-from-query.md)
* [.move 익스텐트](../management/extents-commands.md#move-extents)
* [.바꾸기 익스텐트](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>업데이트 정책 개체

테이블에는 0개, 하나 이상의 업데이트 정책 개체가 연결되어 있을 수 있습니다.
이러한 각 개체는 JSON 속성 백으로 표시되며 다음 속성이 정의됩니다.

|속성 |Type |Description  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |업데이트 정책이 활성화되어 있는 경우 상태(true) 또는 사용 안 함(false)                                                                                                                               |
|원본                        |`string`|호출할 업데이트 정책을 트리거하는 테이블 이름                                                                                                                                 |
|쿼리                         |`string`|업데이트에 대한 데이터를 생성하는 데 사용되는 Kusto CSL 쿼리                                                                                                                           |
|IsTransactional               |`bool`  |업데이트 정책이 트랜잭션인지 아닌지(기본값은 false). 트랜잭션 업데이트 정책을 실행하지 않으면 원본 테이블도 새 데이터로 업데이트되지 않습니다.   |
|전파속성  |`bool`  |소스 테이블에 들어가는 동안 지정된 인비레이션 속성(익스텐트 태그 및 생성 시간)이 파생 테이블의 속성에도 적용되어야 하는 경우 상태를 지정합니다.                 |

> [!NOTE]
>
> * 업데이트 정책이 정의된 원본 테이블과 테이블은 **동일한 데이터베이스에 있어야 합니다.**
> * 쿼리에는 데이터베이스 간 또는 클러스터 간 쿼리가 포함되지 **않을** 수 있습니다.
> * 쿼리는 저장된 함수를 호출할 수 있습니다.
> * 새로 받은 레코드만 을 다루기 위해 쿼리의 범위가 자동으로 조정됩니다.
> * 계단식 업데이트가 허용됩니다(TableA --`[update]`-> TableB --`[update]`-> TableC --`[update]`-> ...)
> * 업데이트 정책이 여러 테이블에 대해 순환 방식으로 정의되는 경우 런타임에 검색되고 업데이트 체인이 잘린 경우(즉, 영향을 받는 테이블 체인의 각 테이블에 한 번만 데이터가 수집됩니다).
> * 정책 `Source` `Query` 부분(또는 후자에서 참조하는 함수)에서 테이블을 참조할 때는 **테이블의** 정규화된 이름(의미, 사용 `TableName` 및 사용 **안** `database("DatabaseName").TableName` 함)을 `cluster("ClusterName").database("DatabaseName").TableName`사용하지 않도록 해야 합니다.
> * 업데이트 정책의 쿼리는 활성화된 행 수준 [보안 정책을](./rowlevelsecuritypolicy.md) 사용하는 테이블을 참조할 수 없습니다.
> * 업데이트 정책의 일부로 실행되는 쿼리에는 [RestrictedViewAccess 정책이](restrictedviewaccesspolicy.md) 활성화된 테이블에 대한 읽기 액세스 권한이 **없습니다.**
> * `PropagateIngestionProperties`인기 작업에서만 적용됩니다. `.move extents` 또는 `.replace extents` 명령의 일부로 업데이트 정책이 트리거되면 이 옵션은 영향을 **주지 않습니다.**
> * 업데이트 정책이 `.set-or-replace` 명령의 일부로 호출되면 기본 동작은 파생 테이블의 데이터도 원본 테이블에 있는 것처럼 대체된다는 것입니다.

## <a name="retention-policy-on-the-source-table"></a>원본 테이블의 보존 정책

원본 테이블의 원시 데이터를 유지하지 않도록 업데이트 정책을 트랜잭션으로 설정하는 동안 원본 테이블의 [보존 정책에서](retentionpolicy.md)소프트 삭제 기간을 0으로 설정할 수 있습니다.

이렇게 하면 다음과 같은 결과가 발생합니다.
* 원본 테이블에서 쿼리할 수 없는 원본 데이터입니다.
* 원본 데이터가 수집 작업의 일부로 지속 가능한 저장소에 유지되지 않습니다.
* 작업 성능 향상.
* 소스 테이블의 [익스텐션에서](../management/extents-overview.md) 수행되는 백그라운드 정리 작업에 대해 사후 작업을 활용하는 리소스 의 감소입니다.

## <a name="failures"></a>오류

경우에 따라 원본 테이블에 데이터를 수집하는 데 성공하지만 대상 테이블을 통합하는 동안 업데이트 정책이 실패합니다.

정책이 업데이트되는 동안 발생한 오류는 다음과 같이 [.show 인비전에실패 명령을](../management/ingestionfailures.md)사용하여 검색할 수 있습니다.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

오류는 다음과 같이 처리됩니다.

* **비트랜잭션 정책**: Kusto에서 오류를 무시합니다. 재시도는 데이터 소유자의 책임입니다.  
* **트랜잭션 정책**: 업데이트를 트리거한 원래 의 작업도 실패합니다. 원본 테이블과 데이터베이스는 새 데이터로 수정되지 않습니다.
  * 수집 `pull` 방법(Kusto의 데이터 관리 서비스가 수집 프로세스에 관여하는 경우)이 있는 경우 다음 논리에 따라 Kusto의 데이터 관리 서비스에서 오케스트레이션한 전체 수집 작업에 대한 자동 재시도가 있습니다.
    * 재시도는 (기본 = 2 `DataImporterMaximumRetryPeriod` 일) (기본 `DataImporterMaximumRetryAttempts` = 10) 사이의 가장 빠른 에 도달 할 때까지 수행됩니다.
    * 위의 설정은 KustoOps에 의해 데이터 관리 서비스의 구성에서 변경할 수 있습니다.
    * 백오프 기간은 2 분에서 시작하고 기하 급수적으로 성장 (2 -> 4 -> 8 -> 16 ... 분)
  * 다른 경우에는 재시도가 데이터 소유자의 책임입니다.



## <a name="control-commands"></a>제어 명령

* [.show 테이블 테이블 정책 업데이트를](../management/update-policy.md#show-update-policy) 사용하여 테이블의 현재 업데이트 정책을 표시합니다.
* [.alter 테이블 테이블 테이블 정책 업데이트를](../management/update-policy.md#alter-update-policy) 사용하여 테이블의 현재 업데이트 정책을 설정합니다.
* [.alter-merge 테이블 TABLE 정책 업데이트를](../management/update-policy.md#alter-merge-table-table-policy-update) 사용하여 테이블의 현재 업데이트 정책에 부가합니다.
* [.delete 테이블 테이블 테이블 정책 업데이트를](../management/update-policy.md#delete-table-table-policy-update) 사용하여 테이블의 현재 업데이트 정책에 추가합니다.

## <a name="testing-an-update-policys-performance-impact"></a>업데이트 정책의 성능 영향 테스트

업데이트 정책을 정의하면 소스 테이블에 대한 모든 구성에 영향을 미치기 때문에 Kusto 클러스터의 성능에 영향을 줄 수 있습니다. 업데이트 정책의 `Query` 일부가 잘 수행되도록 최적화되어 있는 것이 좋습니다.
업데이트 정책이 해당 `Query` 부분에서 사용하는 정책 및/또는 함수를 만들거나 변경하기 전에 특정 및 기존 익스텐션에 대해 호출하여 업데이트 정책의 추가 성능 영향을 테스트할 수 있습니다.

다음 예제에서는 다음을 가정합니다.

* 원본 테이블 이름(업데이트 정책의 `Source` 속성)은 `MySourceTable`입니다.
* 업데이트 `Query` 정책의 속성은 을 호출하는 함수를 `MyFunction()`호출합니다.

[.show 쿼리를](../management/queries.md)사용하여 다음 쿼리의 리소스 사용량(CPU, 메모리 등)을 평가하고 쿼리를 여러 번 실행할 수 있습니다.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```