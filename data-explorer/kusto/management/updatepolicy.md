---
title: 원본에 추가 된 데이터에 대 한 kusto 업데이트 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 28e88b71b5d7a2f8729e2f9eef416ee5804a2880
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337638"
---
# <a name="update-policy-overview"></a>정책 업데이트 개요

[업데이트 정책은](update-policy.md) 원본 테이블에 삽입 된 데이터에서 실행 되는 변환 쿼리를 기반으로 새 데이터를 원본 테이블에 삽입할 때마다 kusto가 대상 테이블에 데이터를 자동으로 추가 하도록 지시 합니다.

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Azure 데이터 탐색기의 업데이트 정책 개요":::

예를 들어 정책을 사용 하 여 다른 테이블의 필터링 된 보기를 하나의 테이블에 만들 수 있습니다. 새 테이블에는 다른 스키마, 보존 정책 등이 있을 수 있습니다. 

업데이트 정책은 일반적인 수집과 동일한 제한 사항 및 모범 사례를 따릅니다. 이 정책은 클러스터 크기를 확장 하 고, ingestions가 큰 동료 ks에서 수행 되는 경우 보다 효율적으로 작동 합니다.

> [!NOTE]
> 원본 테이블과 업데이트 정책이 정의 된 테이블은 동일한 데이터베이스에 있어야 합니다.
> 업데이트 정책 함수 스키마와 대상 테이블 스키마는 열 이름, 형식 및 순서와 일치 해야 합니다.

## <a name="update-policys-query"></a>정책 쿼리 업데이트

업데이트 정책 쿼리는 새로 수집 레코드를 포함 하도록 자동으로 범위가 지정 된 특수 모드에서 실행 되며,이 쿼리의 일부로 원본 테이블의 수집 데이터를 쿼리할 수 없습니다. 그러나 트랜잭션 업데이트 정책의 "경계"에 있는 데이터 수집는 단일 트랜잭션에서 쿼리에 사용할 수 있게 됩니다. 업데이트 정책이 대상 테이블에 정의 되어 있기 때문에 데이터를 하나의 원본 테이블로 수집 해당 데이터에 대해 여러 개의 쿼리가 실행 될 수 있습니다. 여러 업데이트 정책의 실행 순서는 정의 되지 않습니다. 

### <a name="query-limitations"></a>쿼리 제한 사항 

* 쿼리에서는 저장 된 함수를 호출할 수 있지만 데이터베이스 간 또는 클러스터 간 쿼리는 포함할 수 없습니다. 
* 업데이트 정책의 일부로 실행 되는 쿼리에는 [RestrictedViewAccess 정책이](restrictedviewaccesspolicy.md) 사용 하도록 설정 된 테이블에 대 한 읽기 액세스 권한이 없거나 [행 수준 보안 정책이](rowlevelsecuritypolicy.md) 사용 하도록 설정 되어 있지 않습니다.
* `Source` `Query` 정책의 일부 또는 파트에서 참조 하는 함수에서 테이블을 참조할 때 `Query` :
   * 테이블의 정규화 된 이름을 사용 하지 마세요. 대신 `TableName` 을(를) 사용하세요. 
   * 또는를 사용 하지 마세요 `database("DatabaseName").TableName` `cluster("ClusterName").database("DatabaseName").TableName` .

> [!WARNING]
> 업데이트 정책에서 잘못 된 쿼리를 정의 하면 데이터가 원본 테이블로 수집 되지 않을 수 있습니다.

## <a name="the-update-policy-object"></a>업데이트 정책 개체

테이블에는 0 개 이상의 업데이트 정책 개체가 연결 되어 있을 수 있습니다.
이러한 각 개체는 다음과 같은 속성이 정의 된 JSON 속성 모음으로 표시 됩니다.

|속성 |유형 |설명  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |업데이트 정책 사용 (true) 또는 사용 안 함 (false)에 대 한 상태                                                                                                                               |
|원본                        |`string`|호출할 업데이트 정책을 트리거하는 테이블의 이름입니다.                                                                                                                                 |
|쿼리                         |`string`|업데이트에 대 한 데이터를 생성 하는 데 사용 되는 Kusto CSL 쿼리                                                                                                                           |
|IsTransactional               |`bool`  |업데이트 정책이 트랜잭션 (기본값은 false) 인지 여부를 나타내는입니다. 트랜잭션 업데이트 정책을 실행 하지 못하면 원본 테이블이 새 데이터로 업데이트 되지 않습니다.   |
|PropagateIngestionProperties  |`bool`  |원본 테이블로 수집 하는 중에 지정한 수집 속성 (익스텐트 태그 및 만든 시간)이 파생 테이블의 테이블에도 적용 되어야 한다는 것을 말합니다.                 |

> [!NOTE]
> 연속 업데이트를 사용할 수 있습니다 ( `TableA` → `TableB` → `TableC` → ...).
>
> 그러나 업데이트 정책이 순환 방식으로 여러 테이블에 대해 정의 되는 경우 업데이트 체인이 잘립니다. 이 문제는 런타임에 검색 됩니다. 데이터는 영향을 받는 테이블 체인의 각 테이블에 한 번만 수집 됩니다.

## <a name="update-policy-commands"></a>정책 업데이트 명령

업데이트 정책을 제어 하는 명령은 다음과 같습니다.

* [. show Table *TableName* 정책 업데이트](update-policy.md#show-update-policy) 는 테이블의 현재 업데이트 정책을 표시 합니다.
* [. alter Table *TableName* 정책 업데이트](update-policy.md#alter-update-policy) 는 테이블의 현재 업데이트 정책을 설정 합니다.
* [. alter-merge Table *TableName* 정책 업데이트](update-policy.md#alter-merge-table-tablename-policy-update) 는 테이블의 현재 업데이트 정책에 추가 됩니다.
* [. 테이블 삭제 *TableName* 정책 업데이트](update-policy.md#delete-table-tablename-policy-update) 는 테이블의 현재 업데이트 정책에 추가 됩니다.

## <a name="update-policy-is-initiated-following-ingestion"></a>수집 후 업데이트 정책을 시작 합니다.

업데이트 정책은 다음 명령 중 하나를 사용 하 여 데이터를 수집 하거나로 이동 하는 경우에 적용 됩니다.

* [. 수집 (pull)](../management/data-ingestion/ingest-from-storage.md)
* [. 수집 (인라인)](../management/data-ingestion/ingest-inline.md)
* [. set |. append |. set 또는-append |. set 또는-replace](../management/data-ingestion/ingest-from-query.md)
  * 업데이트 정책을 명령의 일부로 호출 하는 경우  `.set-or-replace` 기본 동작은 파생 테이블의 데이터가 원본 테이블에서와 동일한 방식으로 대체 된다는 것입니다.
* [.move extents](./move-extents.md)
* [.replace extents](./replace-extents.md)
  * 이 `PropagateIngestionProperties` 명령은 수집 작업에만 적용 됩니다. 업데이트 정책이 `.move extents` 또는 명령의 일부로 트리거되면 `.replace extents` 이 옵션은 영향을 주지 않습니다.

## <a name="regular-ingestion-using-update-policy"></a>업데이트 정책을 사용한 일반 수집

업데이트 정책은 다음 조건이 충족 될 때 일반적인 수집 처럼 동작 합니다.

* 원본 테이블은 중요 한 데이터가 자유 텍스트 열로 지정 된 높은 수준의 추적 테이블입니다. 
* 업데이트 정책이 정의 된 대상 테이블은 특정 추적 선만 허용 합니다.
* 테이블에는 [구문 분석 연산자](../query/parseoperator.md)에 의해 생성 된 원본 자유 텍스트 데이터를 변환 하는 잘 구성 된 스키마가 있습니다.

## <a name="zero-retention-on-source-table"></a>원본 테이블에 대 한 보존 0

경우에 따라 데이터는 원본 테이블에 대 한 단계별 수집 대상 테이블에만 표시 되며 원시 데이터를 원본 테이블에 유지 하지 않으려고 할 수 있습니다. 원본 테이블의 [보존 정책](retentionpolicy.md)에서 일시 삭제 기간을 0으로 설정 하 고 업데이트 정책을 트랜잭션으로 설정 합니다. 이 경우 다음을 수행 합니다. 

* 원본 테이블에서 원본 데이터를 쿼리할는 없습니다. 
* 원본 데이터는 수집 작업의 일부로 지속형 저장소에 유지 되지 않습니다. 
* 운영 성능이 향상 됩니다. 
* 백그라운드 정리 작업을 위한 수집 후 리소스가 줄어듭니다. 이러한 작업은 원본 테이블의 [익스텐트에](../management/extents-overview.md) 대해 수행 됩니다.

## <a name="performance-impact"></a>성능에 미치는 영향

업데이트 정책은 Kusto 클러스터의 성능에 영향을 줄 수 있습니다. 업데이트 정책은 원본 테이블에 대 한 모든 수집에 영향을 줍니다. 여러 데이터 익스텐트의 수집에는 대상 테이블의 수가 곱해집니다. 따라서 `Query` 업데이트 정책의 일부는 잘 작동 하도록 최적화 되어 있어야 합니다. 수집 작업에 대 한 업데이트 정책의 추가 성능 영향을 테스트할 수 있습니다. 해당 부분에서 사용 하는 정책이 나 함수를 만들거나 변경 하기 전에 특정 및 기존 범위에서 정책을 호출 합니다 `Query` .

### <a name="evaluate-resource-usage"></a>리소스 사용 평가

다음 시나리오에서 [. show 쿼리](../management/queries.md)를 사용 하 여 리소스 사용 (CPU, 메모리 등)을 평가 합니다.
* 원본 테이블 이름 ( `Source` 업데이트 정책의 속성)은 `MySourceTable` 입니다.
* `Query`업데이트 정책의 속성은 라는 함수를 호출 합니다 `MyFunction()` .

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>오류

기본적으로 업데이트 정책 실행 실패는 원본 테이블에 대 한 데이터 수집에 영향을 주지 않습니다. 그러나 업데이트 정책이 다음과 같이 정의 된 경우 `IsTransactional` 정책 실행에 실패 하면 원본 테이블에 대 한 데이터 수집이 실패 합니다. 원본 테이블에 대 한 데이터 수집이 성공 하지만 대상 테이블로 수집 하는 동안 업데이트 정책이 실패 하는 경우도 있습니다.

정책을 업데이트 하는 동안 발생 하는 오류는 수집 [실패 표시 명령을](../management/ingestionfailures.md)사용 하 여 검색할 수 있습니다.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>오류 처리

#### <a name="non-transactional-policy"></a>비트랜잭션 정책 

Kusto에서 오류를 무시 합니다. 재시도는 데이터 수집 프로세스 소유자의 책임입니다.  

#### <a name="transactional-policy"></a>트랜잭션 정책

업데이트를 트리거한 원래 수집 작업도 실패 합니다. 원본 테이블과 데이터베이스는 새 데이터를 사용 하 여 수정 되지 않습니다.
수집 방법이 `pull` (kusto의 데이터 관리 서비스는 수집 프로세스에 포함 됨) 인 경우 다음 논리에 따라 전체 수집 작업을 자동으로 다시 시도 합니다 .이 작업은 kusto의 데이터 관리 서비스에 의해 오케스트레이션.
* 다시 시도는 `DataImporterMaximumRetryPeriod` (기본값 = 2 일)과 `DataImporterMaximumRetryAttempts` (기본값 = 10) 사이에 도달할 때까지 수행 됩니다.
* 위의 두 설정은 데이터 관리 서비스의 구성에서 변경할 수 있습니다.
* 백오프 기간은 2 분 후에 시작 하 고 급격 하 게 증가 합니다 (2-> 4-> 8-> 16 ... 내

어떤 경우 든 다시 시도는 데이터 소유자의 책임입니다.