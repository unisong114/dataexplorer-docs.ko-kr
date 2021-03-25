---
title: Kusto 쿼리 수집(set, append, replace) - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer에서 수행되는 쿼리(.set, .append, .set-or-append, .set-or-replace)의 수집에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: fe2d9b54970bbbd6ce9b5ee22fc6460c30b34a76
ms.sourcegitcommit: 61f0c37a8c9aa97cc09715466cc639272174325c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103566302"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>쿼리에서 수집(.set, .append, .set-or-append, .set-or-replace)

이러한 명령은 쿼리 또는 제어 명령을 실행하고 쿼리 결과를 테이블에 수집합니다. 이러한 명령의 차이점은 기존 테이블과 데이터 또는 없는 테이블과 데이터를 처리하는 방법에 있습니다.

|명령          |테이블이 있는 경우                     |테이블이 없는 경우                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |명령이 실패합니다.                  |테이블이 만들어지고 데이터가 수집됩니다.|
|`.append`        |데이터가 테이블에 추가됩니다.      |명령이 실패합니다.                        |
|`.set-or-append` |데이터가 테이블에 추가됩니다.      |테이블이 만들어지고 데이터가 수집됩니다.|
|`.set-or-replace`|데이터가 테이블의 데이터를 대체합니다.|테이블이 만들어지고 데이터가 수집됩니다.|

**구문**

`.set` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...`])`] `<|` *QueryOrCommand*

`.set-or-append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.set-or-replace` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

**인수**

* `async`: 지정되면 명령을 즉시 반환하고 백그라운드에서 수집을 계속합니다. 명령 결과에는 `.show operations` 명령에 사용할 수 있는 `OperationId` 값이 포함되어 수집 완료 상태 및 결과를 검색할 수 있습니다.
* *TableName*: 데이터를 수집할 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트에서 데이터베이스와 관련이 있습니다.
* *PropertyName*, *PropertyValue*: 수집 프로세스에 영향을 주는 수집 속성의 수입니다.

 지원되는 수집 속성은 다음과 같습니다.

|속성        |설명|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 수집되는 데이터 범위를 만드는 시간에 사용할 ISO8601 문자열 형식의 날짜/시간 값입니다. 지정하지 않으면 현재 값(`now()`)이 사용됩니다. 지정된 경우 대상 테이블의 유효한 [익스텐트 병합 정책](../mergepolicy.md)의 `Lookback` 속성이 지정된 값과 일치하는지 확인합니다.|
|`extend_schema`  | 지정되면 테이블의 스키마를 확장하도록 명령에 지시하는 부울 값입니다. 기본값은 "false"입니다. 이 옵션은 `.append`, `.set-or-append` 및 `set-or-replace` 명령에만 적용됩니다. 허용되는 유일한 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|
|`recreate_schema`  | 부울 값입니다. 지정되면 명령에서 테이블의 스키마를 다시 만들 수 있는지 여부를 설명합니다. 기본값은 "false"입니다. 이 옵션은 *set-or-replace* 명령에만 적용됩니다. 이 옵션 및 extend_schema 속성이 모두 설정되면 이 옵션이 extend_schema 속성보다 우선적으로 적용됩니다.|
|`folder`         | 테이블에 할당할 폴더입니다. 테이블이 이미 있으면 이 속성은 테이블의 폴더를 덮어씁니다.|
|`ingestIfNotExists`   | 문자열 값입니다. 지정되면 `ingest-by:` 태그로 지정된 동일한 값의 데이터가 테이블에 이미 있는 경우 수집이 성공하지 못합니다.|
|`policy_ingestiontime`   | 부울 값입니다. 지정되면 이 명령에서 만드는 테이블에서 [수집 시간 정책](../../management/ingestiontime-policy.md)을 사용하도록 설정할지 여부를 설명합니다. 기본값은 "true"입니다.|
|`tags`   | 수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다.|
|`docstring`   | 테이블을 문서화하는 문자열입니다.|

 명령의 동작을 제어하는 속성은 다음과 같습니다.

|속성        |형식    |설명|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |명령이 쿼리를 동시에 실행하는 모든 노드에서 수집함을 나타냅니다. 기본값은 "false"입니다.  아래 설명 부분을 참조하세요.|

* *QueryOrCommand*: 결과를 수집할 데이터로 사용할 쿼리 또는 제어 명령의 텍스트입니다.

> [!NOTE]
> `.show` 제어 명령만 지원됩니다.

**주의**

* 테이블의 데이터가 있는 경우 `.set-or-replace`는 이를 대체합니다. 기존 데이터 분할을 삭제하거나 대상 테이블이 아직 없는 경우 새로 만듭니다.
  `extend_schema` 또는 `recreate_schema` 수집 속성 중 하나를 "true"로 설정하지 않으면 테이블 스키마가 유지됩니다. 스키마가 수정되면 자체 트랜잭션에서 실제 데이터를 수집하기 전에 해당 스키마가 발생합니다. 데이터 수집 실패는 스키마가 수정되지 않았음을 의미하지 않습니다.
* `extend_schema` 수집 속성이 "true"로 설정되지 않으면 `.set-or-append` 및 `.append` 명령에서 스키마를 유지합니다. 스키마가 수정되면 자체 트랜잭션에서 실제 데이터를 수집하기 전에 해당 스키마가 발생합니다. 데이터 수집 실패는 스키마가 수정되지 않았음을 의미하지 않습니다.
* 수집 데이터를 수집 작업당 1GB 미만으로 제한하는 것이 좋습니다. 필요한 경우 여러 수집 명령을 사용할 수 있습니다.
* 데이터 수집은 쿼리 실행을 포함하여 클러스터의 동시 작업에 영향을 줄 수 있는 리소스 집약적인 작업입니다. 너무 많은 명령을 동시에 실행하지 마세요.
* 결과 집합 스키마를 대상 테이블의 스키마와 일치시키는 경우 열 형식을 기준으로 비교합니다. 일치하는 열 이름이 없습니다. 쿼리 결과 스키마 열이 테이블과 동일한 순서로 되어 있는지 확인합니다. 그렇지 않으면 데이터가 잘못된 열에 수집됩니다.
* `distributed` 플래그를 "true"로 설정하면 쿼리에서 생성되는 데이터 양이 크고 1GB를 초과하며 쿼리를 직렬화할 필요가 없으므로 여러 노드에서 출력을 동시에 생성할 수 있습니다.
  쿼리 결과가 작으면 작은 데이터 분할을 불필요하게 많이 생성할 수 있으므로 이 플래그를 사용하지 마세요.

**예제** 

:::no-loc text="LogsTable":::과 동일한 스키마가 있고 지난 1 시간의 모든 오류 레코드가 포함되는 :::no-loc text="RecentErrors":::라는 새 테이블을 데이터베이스에 만듭니다.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

"ExtentId"라는 단일 열이 있고 30일 이전에 만들어진 데이터베이스의 모든 범위에 대한 범위 ID가 포함되는 "OldExtents"라는 새 테이블을 데이터베이스에 만듭니다. 데이터베이스에는 "MyExtents"라는 기존 테이블이 있습니다.

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

"ExtentId"라는 단일 열이 있고 30일 이전에 만들어진 데이터베이스의 모든 범위에 대한 범위 ID가 포함되는 현재 데이터베이스의 "OldExtents"라는 기존 테이블에 데이터를 추가합니다.
"MyExtents"라는 기존 테이블을 기반으로 하여 `tagA` 및 `tagB` 태그로 새 범위를 표시합니다.

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

데이터를 현재 데이터베이스의 "OldExtents" 테이블에 추가하거나 테이블이 아직 없는 경우 새로 만듭니다. `ingest-by:myTag`를 사용하여 새 범위에 대한 태그를 지정합니다. "MyExtents"라는 기존 테이블을 기반으로 하여 `ingest-by:myTag` 태그로 지정된 범위가 테이블에 아직 포함되지 않은 경우에만 이 작업을 수행합니다.

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

현재 데이터베이스의 "OldExtents" 테이블에 있는 데이터를 바꾸거나 테이블이 아직 없는 경우 새로 만듭니다. `ingest-by:myTag`를 사용하여 새 범위에 대한 태그를 지정합니다.

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

데이터를 현재 데이터베이스의 "OldExtents" 테이블에 추가하고, 만들어진 범위를 만드는 시간을 과거의 특정 날짜/시간으로 설정합니다.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**반환 출력**
 
`.set` 또는 `.append` 명령으로 인해 만들어진 범위에 대한 정보를 반환합니다.

**예제 출력**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
