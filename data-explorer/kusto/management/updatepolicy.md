---
title: 원본에 추가 된 데이터에 대 한 kusto 업데이트 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 072c908109fecb695a8961c546deb756caf830ab
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868708"
---
# <a name="update-policy"></a>정책 업데이트

업데이트 정책은 새 데이터가 **원본 테이블**에 삽입 될 때마다 자동으로 데이터를 추가 하도록 kusto에 지시 하는 **대상 테이블** 에 설정 됩니다. 업데이트 정책의 **쿼리** 는 원본 테이블에 삽입 된 데이터에 대해 실행 됩니다. 이를 통해 다른 테이블의 필터링 된 뷰로 한 테이블을 만들 수 있습니다. 예를 들어 다른 스키마, 보존 정책 등이 있을 수 있습니다.

기본적으로 업데이트 정책 실행 실패는 원본 테이블에 대 한 데이터 수집에 영향을 주지 않습니다. 업데이트 정책이 **트랜잭션**으로 정의 된 경우 업데이트 정책을 실행 하지 않으면 원본 테이블에 대 한 데이터 수집도 실패 합니다. 업데이트 정책에서 잘못 된 쿼리를 정의 하는 등의 일부 사용자 오류는 데이터를 원본 테이블로 수집 **하지 않을 수** 있으므로이 작업을 수행 하는 경우 주의 해야 합니다. 트랜잭션 업데이트 정책의 "경계"에 있는 데이터 수집 단일 트랜잭션의 쿼리에 사용할 수 있게 됩니다.

업데이트 정책의 쿼리는 특수 모드에서 실행 되며,이 모드에서는 원본 테이블에 대 한 새로 수집 데이터만 "확인" 합니다. 이 쿼리의 일부로 원본 테이블의 이미 수집 데이터를 쿼리할 수 없습니다. 이렇게 하면 2 시간 ingestions 빠르게 발생 합니다.

업데이트 정책이 대상 테이블에 정의 되어 있기 때문에 데이터를 하나의 원본 테이블로 수집 해당 데이터에 대해 여러 개의 쿼리가 실행 될 수 있습니다. 업데이트 정책 실행 순서는 정의 되지 않습니다.

예를 들어 원본 테이블이 자유 텍스트 열로 서식이 지정 된 흥미로운 데이터를 포함 하는 높은 수준의 추적 테이블 이라고 가정 합니다. 또한 업데이트 정책이 정의 된 대상 테이블은 특정 추적 줄만 허용 하 고, Kusto의 [parse 연산자](../query/parseoperator.md)를 사용 하 여 원래 자유 텍스트 데이터를 변환 하는 잘 구성 된 스키마를 사용 한다고 가정 합니다.

업데이트 정책은 일반적인 수집과 유사 하 게 작동 하며 동일한 제한 사항 및 모범 사례를 따릅니다. 예를 들어, 클러스터의 크기를 확장 하 고, 큰 ingestions ks에서 작업을 수행 하는 경우 보다 효율적으로 작동 합니다.

## <a name="commands-that-trigger-the-update-policy"></a>업데이트 정책을 트리거하는 명령

업데이트 정책은 다음 명령 중 하나를 사용 하 여 데이터를 수집 하거나로 이동 하는 경우에 적용 됩니다.

* [. 수집 (pull)](../management/data-ingestion/ingest-from-storage.md)
* [. 수집 (인라인)](../management/data-ingestion/ingest-inline.md)
* [. set |. append |. set 또는-append |. set 또는-replace](../management/data-ingestion/ingest-from-query.md)
* [. 익스텐트 이동](../management/extents-commands.md#move-extents)
* [. 익스텐트 바꾸기](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>업데이트 정책 개체

테이블에는 0 개 이상의 업데이트 정책 개체가 연결 되어 있을 수 있습니다.
이러한 각 개체는 다음과 같은 속성이 정의 된 JSON 속성 모음으로 표시 됩니다.

|속성 |Type |Description  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |업데이트 정책 사용 (true) 또는 사용 안 함 (false)에 대 한 상태                                                                                                                               |
|원본                        |`string`|호출할 업데이트 정책을 트리거하는 테이블의 이름입니다.                                                                                                                                 |
|쿼리                         |`string`|업데이트에 대 한 데이터를 생성 하는 데 사용 되는 Kusto CSL 쿼리                                                                                                                           |
|IsTransactional               |`bool`  |업데이트 정책이 트랜잭션 (기본값은 false) 인지 여부를 나타내는입니다. 트랜잭션 업데이트 정책을 실행 하지 않으면 원본 테이블도 새 데이터로 업데이트 되지 않습니다.   |
|PropagateIngestionProperties  |`bool`  |원본 테이블에 대 한 수집 중에 지정 된 수집 속성 (익스텐트 태그 및 만든 시간)을 파생 테이블의 테이블에도 적용 해야 합니다.                 |

## <a name="notes"></a>참고

* 쿼리는 새로 수집 레코드만 포함 하도록 자동으로 범위가 지정 됩니다.
* 쿼리가 저장 된 함수를 호출할 수 있습니다.
* 연속 업데이트를 사용할 수`TableA` 있습니다 `TableB` ( `TableC` → → → ...).
* 업데이트 정책을 `.set-or-replace` 명령의 일부로 호출 하는 경우 기본 동작은 파생 테이블의 데이터도 원본 테이블에 있는 것 처럼 대체 된다는 것입니다.

## <a name="limitations"></a>제한 사항

* 원본 테이블과 업데이트 정책이 정의 된 테이블은 **동일한 데이터베이스에 있어야**합니다.
* 이 쿼리에는 데이터베이스 간 및 클러스터 간 쿼리가 포함 **되지** 않을 수 있습니다.
* 업데이트 정책이 순환 방식으로 여러 테이블에 대해 정의 되는 경우이는 런타임에 검색 되 고 업데이트 체인이 잘립니다. 즉, 데이터는 영향을 받는 테이블 체인의 각 테이블에 한 번만 수집 됩니다.
* 정책의 `Source` `Query` 일부 (또는 후자에서 참조 하는 함수)에서 테이블을 참조 하는 경우 테이블의 정규화 된 이름을 사용 **하지** 않아야 합니다 (즉, 사용 및 `TableName` 사용 **안** `database("DatabaseName").TableName` `cluster("ClusterName").database("DatabaseName").TableName`함).
* 업데이트 정책의 일부로 실행 되는 쿼리에는 [RestrictedViewAccess 정책이](restrictedviewaccesspolicy.md) 설정 된 테이블에 대 한 읽기 권한이 **없습니다** .
* 업데이트 정책 쿼리에서는 [행 수준 보안 정책이](./rowlevelsecuritypolicy.md) 설정 된 테이블을 참조할 수 없습니다.
* `PropagateIngestionProperties`는 수집 작업에만 적용 됩니다. 업데이트 정책이 `.move extents` 또는 `.replace extents` 명령의 일부로 트리거되면이 옵션은 영향을 주지 **않습니다** .

## <a name="retention-policy-on-the-source-table"></a>원본 테이블에 대 한 보존 정책

따라서 원본 테이블에서 원시 데이터를 유지 하지 않기 때문에 업데이트 정책을 트랜잭션으로 설정 하는 동안 원본 테이블의 [보존 정책](retentionpolicy.md)에서 일시 삭제 기간을 0으로 설정할 수 있습니다.

그러면 다음과 같은 결과가 나타납니다.
* 원본 테이블에서 원본 데이터를 쿼리할 수 없습니다.
* 원본 데이터가 수집 작업의 일부로 지속형 저장소에 유지 되지 않습니다.
* 작업 성능 향상.
* 리소스 감소는 원본 테이블의 [익스텐트에](../management/extents-overview.md) 대해 수행 되는 백그라운드 정리 작업에 대해 사후 수집을 활용 합니다.

## <a name="failures"></a>오류

원본 테이블에 대 한 데이터 수집이 성공 하지만 대상 테이블로 수집 하는 동안 업데이트 정책이 실패 하는 경우도 있습니다.

정책을 업데이트 하는 동안 발생 한 오류는 다음과 같이 수집 [실패 표시 명령을](../management/ingestionfailures.md)사용 하 여 검색할 수 있습니다.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

오류는 다음과 같이 처리 됩니다.

* **비트랜잭션 정책**: Kusto에 의해 오류가 무시 됩니다. 다시 시도는 데이터 소유자의 책임입니다.  
* **트랜잭션 정책**: 업데이트를 트리거한 원래 수집 작업도 실패 합니다. 원본 테이블과 데이터베이스는 새 데이터를 사용 하 여 수정 되지 않습니다.
  * 수집 방법이 `pull` (kusto의 데이터 관리 서비스는 수집 프로세스에 관련 됨) 인 경우 다음 논리에 따라 kusto의 데이터 관리 서비스에서 오케스트레이션 전체 수집 작업에 대해 자동화 된 재시도를 수행 합니다.
    * (기본값 = 2 일)과 ( `DataImporterMaximumRetryPeriod` 기본값 = 10) 사이에서 가장 `DataImporterMaximumRetryAttempts` 이른 시간에 도달할 때까지 다시 시도를 수행 합니다.
    * 위의 설정 데이터 관리 서비스의 구성에서 KustoOps로 변경할 수 있습니다.
    * 백오프 기간은 2 분부터 시작 하 여 급격 하 게 증가 합니다 (2-> 4-> 8-> 16 ... 내
  * 어떤 경우 든 다시 시도는 데이터 소유자의 책임입니다.



## <a name="control-commands"></a>제어 명령

* 테이블의 현재 업데이트 정책을 표시 하려면 테이블 [테이블 정책 업데이트 표시를 사용 합니다.](../management/update-policy.md#show-update-policy)
* 을 사용 하 여 테이블의 현재 업데이트 정책을 설정 [합니다.](../management/update-policy.md#alter-update-policy)
* 을 사용 하 여 테이블의 현재 업데이트 정책에 추가할 수 [있습니다.](../management/update-policy.md#alter-merge-table-table-policy-update)
* 테이블의 현재 업데이트 정책에 추가 하려면를 사용 하 여 테이블 [테이블 정책 업데이트를 삭제](../management/update-policy.md#delete-table-table-policy-update) 합니다.

## <a name="testing-an-update-policys-performance-impact"></a>업데이트 정책의 성능 영향 테스트

업데이트 정책을 정의 하면 원본 테이블에 대 한 모든 수집에 영향을 주므로 Kusto 클러스터의 성능에 영향을 줄 수 있습니다. 업데이트 정책 `Query` 부분을 최적화 하 여 잘 수행 하는 것이 좋습니다.
정책 및/또는 해당 `Query` 파트에서 사용 하는 함수를 만들거나 변경 하기 전에 특정 및 기존 범위에서이를 호출 하 여 수집 작업에 대 한 업데이트 정책의 추가 성능 영향을 테스트할 수 있습니다.

다음 예제에서는 다음을 가정합니다.

* 원본 테이블 이름 (업데이트 정책의 `Source` 속성)은 `MySourceTable`입니다.
* 업데이트 `Query` 정책의 속성은 라는 `MyFunction()`함수를 호출 합니다.

[. Show 쿼리](../management/queries.md)를 사용 하 여 다음 쿼리의 리소스 사용 (CPU, 메모리 등)을 평가 하 고이를 여러 번 실행할 수 있습니다.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```
