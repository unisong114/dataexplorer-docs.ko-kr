---
title: 저장 된 쿼리 결과 (미리 보기)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 저장 된 쿼리 결과를 만들고 사용 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
ms.date: 12/3/2020
ms.openlocfilehash: fb998499205be7645f011fe727f5e37495ff3697
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749311"
---
# <a name="stored-query-results-preview"></a>저장 된 쿼리 결과 (미리 보기)

고중량 쿼리 결과를 저장 하 고 신속 하 게 검색 합니다.
저장 된 쿼리 결과는 다음과 같은 시나리오에서 유용할 수 있습니다.
* 결과 페이지 매김을 구현 합니다. 저장 된 쿼리 결과는 쿼리를 기반으로 생성 되 고 첫 번째 페이지에 미리 보기가 표시 됩니다. 모든 후속 페이지에는 초기 쿼리를 다시 실행할 필요 없이 미리 계산 된 결과의 다음 부분이 표시 됩니다.
* 데이터 탐색 중에 쿼리 결과를 일시적으로 저장 합니다.

> [!NOTE]
> 저장 된 쿼리 결과는 미리 보기 단계에 있으며 프로덕션 시나리오에는 사용할 수 없습니다. 

저장 된 쿼리 결과는 생성 시점부터 최대 24 시간 동안 액세스할 수 있습니다. 보안 정책 (예: 데이터베이스 액세스, 행 수준 보안 등)의 업데이트는 저장 된 쿼리 결과에 전파 되지 않습니다. 사용자 권한 철회의 경우 [drop stored_query_results](#drop-stored_query_results) 를 사용 합니다. 저장 된 쿼리 결과는 해당 결과를 만든 것과 동일한 보안 주체 id로만 액세스할 수 있습니다. 

저장 된 쿼리 결과는 레코드의 순서가 유지 되지 않는 테이블 처럼 동작 합니다. 결과를 포함 하는 과정을 진행 하려면 쿼리에 고유 ID 열이 포함 되는 것이 좋습니다. 자세한 내용은 [예제](#examples)를 참조하세요. 쿼리에서 반환 된 결과 집합이 여러 개 있는 경우 첫 번째 결과 집합만 저장 됩니다. 

저장 된 쿼리 결과를 사용 하려면 `Database User` 이상의 액세스 역할이 필요 합니다.

## <a name="store-the-results-of-a-query"></a>쿼리 결과 저장

**구문**

`.set``stored_query_result` *StoredQueryResultName* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <| *쿼리*

**인수**

* *StoredQueryResultName*: [엔터티 이름](../query/schema-entities/entity-names.md) 규칙을 준수 하는 저장 된 쿼리 결과 이름입니다.
* *쿼리*: 결과가 저장 되는 잠재적으로 고중량 KQL 쿼리입니다.
* *PropertyName*: (모든 속성은 선택 사항입니다.)
    
    | 속성       | 형식       | 설명       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | 저장 된 쿼리 결과가 만료 되는 시간 (최대 24 시간)을 나타내는 timespan 리터럴입니다. |
    | `previewCount` | `int`      | 미리 보기에 반환할 행의 수입니다. 이 속성을 `0` (기본값)로 설정 하면 명령에서 모든 쿼리 결과 행을 반환 합니다. |
    | `distributed`  | `bool`     | 명령을 실행 하면 쿼리를 실행 하는 모든 노드의 쿼리 결과가 병렬로 저장 됨을 나타냅니다. 기본값은 *true* 입니다. `distributed`플래그를 *false* 로 설정 하면 쿼리에서 생성 되는 데이터의 양이 적거나 많은 클러스터 노드가 많은 경우 작은 데이터 분할 많이 생성 되지 않도록 하는 데 유용 합니다. |

## <a name="retrieve-a-stored-query-result"></a>저장 된 쿼리 결과 검색

저장 된 쿼리 결과를 검색 하려면 쿼리에서 함수를 사용 합니다 `stored_query_result()` .

`stored_query_result``(`' StoredQueryResultName ' `)` `|` ...

## <a name="examples"></a>예제

### <a name="simple-query"></a>단순 쿼리

간단한 쿼리 결과 저장:

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**출력:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

저장 된 쿼리 결과 검색:

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**출력:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>페이지 매김

지난 7 일간 Ad 네트워크 및 날짜별로 클릭을 검색 합니다.

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**출력:**

| Num | 일 | AdNetwork | 개수 |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00:00:00.0000000 | NeoAds | 1002 |
| 2 | 2020-01-01 00:00:00.0000000 | HighHorizons | 543 |
| 3 | 2020-01-01 00:00:00.0000000 | PieAds | 379 |
| ... | ... | ... | ... |

다음 페이지를 검색 합니다.

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**출력:**

| Num | 일 | AdNetwork | 개수 |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00:00:00.0000000 | CoolAds | 301 |
| 101 | 2020-01-01 00:00:00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00:00:00.0000000 | 슈퍼 광고 | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>제어 명령

### <a name="show-stored_query_results"></a>. stored_query_results 표시

활성 저장 된 쿼리 결과에 대 한 정보를 표시 합니다.

>[!NOTE]
> * `DatabaseAdmin`또는 권한이 있는 사용자 `DatabaseMonitor` 는 데이터베이스의 컨텍스트에서 활성 저장 쿼리 결과가 있는지 검사할 수 있습니다.
> * 또는 권한이 있는 사용자는 `DatabaseUser` `DatabaseViewer` 해당 보안 주체가 만든 활성 저장 쿼리 결과가 있는지 검사할 수 있습니다.

#### <a name="syntax"></a>Syntax

`.show` `stored_query_results`

#### <a name="returns"></a>반환

| StoredQueryResultId | Name | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Adventureworks.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 이벤트 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_result"></a>. drop stored_query_result

현재 사용자가 현재 데이터베이스에서 만든 활성 저장 쿼리 결과를 삭제 합니다.

#### <a name="syntax"></a>Syntax

`.drop``stored_query_result` *StoredQueryResultName*

`Database User` 이 명령을 호출 하려면 권한이 필요 합니다.

#### <a name="returns"></a>반환

삭제 된 저장 쿼리 결과에 대 한 정보를 반환 합니다. 예를 들면 다음과 같습니다.

| StoredQueryResultId | Name | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Adventureworks.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 이벤트 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_results"></a>. drop stored_query_results

지정 된 보안 주체에 의해 현재 데이터베이스에 생성 된 활성 저장 쿼리 결과를 삭제 합니다.

`Database Admin` 이 명령을 호출 하려면 권한이 필요 합니다.

#### <a name="syntax"></a>Syntax

`.drop``stored_query_results` `created-by` *PrincipalName*

#### <a name="returns"></a>반환

삭제 된 저장 쿼리 결과에 대 한 정보를 반환 합니다.

예:

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| StoredQueryResultId | Name | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Adventureworks.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 이벤트 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |
| 571f1a76-f5a9-49d4-b339-ba7caac19b46 | 추적 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 5212 | 100000 | 2020-10-07 14:31:01.8271231| 2020-10-08 14:31:01.8271231 |
