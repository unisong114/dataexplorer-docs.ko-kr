---
title: Kusto 쿼리 수집 (설정, 추가, 바꾸기)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 query (. set,. append,. set 또는-replace)의 수집을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: bfa44859987d8f3c4f11221fd8370290f08f9a67
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382049"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>쿼리에서 수집 (. set,. append,. set 또는-append,. set 또는-replace)

이러한 명령은 쿼리 또는 제어 명령을 실행 하 고 쿼리 결과를 테이블에 수집 합니다. 이러한 명령의 차이점은 기존 또는 존재 하지 않는 테이블 및 데이터를 처리 하는 방법입니다.

|명령          |테이블이 존재 하는 경우                     |테이블이 존재 하지 않는 경우                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |명령이 실패 합니다.                  |테이블이 만들어지고 데이터가 수집 됩니다.|
|`.append`        |데이터가 테이블에 추가 됩니다.      |명령이 실패 합니다.                        |
|`.set-or-append` |데이터가 테이블에 추가 됩니다.      |테이블이 만들어지고 데이터가 수집 됩니다.|
|`.set-or-replace`|데이터는 테이블의 데이터를 대체 합니다.|테이블이 만들어지고 데이터가 수집 됩니다.|

**구문**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|` *Queryorcommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Queryorcommand*

**인수**

* `async`: 지정 된 경우 명령은 즉시 반환 되 고 백그라운드에서 계속 수집 됩니다. 명령의 결과에는 명령을 사용 하 여 수집 `OperationId` `.show operation` 완료 상태 및 결과를 검색 하는 데 사용할 수 있는 값이 포함 됩니다.
* *TableName*: 데이터를 수집 하는 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스를 기준으로 합니다.
* *PropertyName*, *PropertyValue*: 수집 프로세스에 영향을 주는 수집 속성 수입니다.

 지원 되는 수집 속성:

|속성        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 수집 데이터 익스텐트의 생성 시 사용할 datetime 값 (ISO8601 문자열로 형식이 지정 됨)입니다. 지정 하지 않으면 현재 값 (now ())이 사용 됩니다.|
|`extend_schema`  | 지정 된 경우 테이블의 스키마를 확장 하는 명령 (기본값은 false)을 지시 하는 부울 값입니다. 이 옵션은. append 또는-append 및 set 또는 replace 명령에만 적용 됩니다. 유일하게 허용되는 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|
|`recreate_schema`  | 지정 된 경우 명령이 테이블의 스키마를 다시 만들 수 있는지 여부 (기본값은 false)를 설명 하는 부울 값입니다. 이 옵션은 set 또는-replace 명령에만 적용 됩니다. 이 옵션은 둘 다 설정 된 경우 extend_schema 속성 보다 우선적으로 적용 됩니다.|
|`folder`         | 테이블에 할당할 폴더입니다. 테이블이 이미 있는 경우이 속성은 테이블의 폴더를 재정의 합니다.|
|`ingestIfNotExists`   | 지정 된 경우 테이블에 동일한 값을 가진 수집 방식: 태그로 태그가 지정 된 데이터가 이미 있는 경우 수집에 성공 하지 못하게 하는 문자열 값입니다.|
|`policy_ingestiontime`   | 지정하면 이 명령으로 만든 테이블에서 [수집 시간 정책](../../management/ingestiontime-policy.md)을 사용하도록 설정할지 여부를 설명하는 부울 값입니다. 기본값은 true입니다.|
|`tags`   | 수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다.|
|`docstring`   | 테이블을 문서화 하는 문자열입니다.|

  또한 명령 자체의 동작을 제어 하는 속성도 있습니다.

|속성        |형식    |설명|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |명령이 쿼리를 병렬로 실행 하는 모든 노드에서 수집을 나타냅니다. 기본값은 `false` 입니다.  아래 설명 부분을 참조 하십시오.|

* *Queryorcommand*: 쿼리 텍스트 또는 결과가 수집할 데이터로 사용 되는 제어 명령입니다.

> [!NOTE]
> `.show`제어 명령만 지원 됩니다.

**주의**

* `.set-or-replace`테이블이 있으면 (기존 데이터 분할 삭제) 테이블의 데이터를 바꾸거나 대상 테이블을 만듭니다 (이미 존재 하지 않는 경우).
  `extend_schema`또는 수집 `recreate_schema` 속성이로 설정 되지 않은 경우 테이블 스키마는 유지 됩니다 `true` . 스키마가 수정 되는 경우이 오류는 실제 데이터를 자체 트랜잭션에서 수집 하기 전에 발생 하므로 데이터 수집에 실패 해도 스키마가 수정 되지 않은 것은 아닙니다.
* `.set-or-append`수집 `.append` 속성이로 설정 되지 않은 경우 및 명령은 스키마를 유지 합니다 `extend_schema` `true` . 스키마가 수정 되는 경우이 오류는 실제 데이터를 자체 트랜잭션에서 수집 하기 전에 발생 하므로 데이터 수집에 실패 해도 스키마가 수정 되지 않은 것은 아닙니다.
* 수집에 대 한 데이터를 수집 작업당 1gb 미만으로 제한 하는 것 **이 좋습니다** . 필요한 경우 여러 수집 명령을 사용할 수 있습니다.
* 데이터 수집은 리소스를 많이 사용 하는 작업이 며 쿼리 실행을 포함 하 여 클러스터의 동시 작업에 영향을 줄 수 있습니다. 이러한 명령을 동시에 함께 실행 하지 않습니다.
* 결과 집합 스키마를 대상 테이블의 결과 집합 스키마와 일치 시키는 경우이 비교는 열 유형을 기반으로 합니다. 열 이름과 일치 하지 않으므로 쿼리 결과 스키마 열이 테이블과 동일한 순서로 되어 있는지 확인 합니다. 그렇지 않으면 데이터가 잘못 된 열로 수집 됩니다.
* 플래그를 `distributed` 로 설정 하면 `true` 쿼리에 의해 생성 되는 데이터 양이 크고 (1gb의 데이터를 초과 함) 쿼리에 serialization이 **and** 필요 하지 않습니다. 따라서 여러 노드가 동시에 출력을 생성할 수 있습니다.
  쿼리 결과가 작은 경우이 플래그를 사용 하지 않는 것이 좋습니다 .이 플래그를 사용 하는 것은 불필요 한 작은 데이터 분할 많이 생성 될 수 있기 때문입니다.

**예** 

현재 데이터베이스에 "LogsTable"와 동일한 스키마를 가지 며 지난 1 시간의 모든 오류 레코드를 포함 하는 "RecentErrors" 라는 새 테이블을 만듭니다.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

단일 열 ("ExtentId")이 있는 현재 데이터베이스에 "OldExtents" 라는 새 테이블을 만들고, "MyExtents" 이라는 기존 테이블을 기반으로 30 일 이전에 생성 된 데이터베이스의 모든 익스텐트의 익스텐트 Id를 보유 합니다.

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

단일 열 ("ExtentId")이 있는 현재 데이터베이스에 있는 "OldExtents" 라는 기존 테이블에 데이터를 추가 하 고 30 일 이전에 만든 데이터베이스에 있는 모든 익스텐트의 익스텐트 Id를 유지 하는 동시에 `tagA` `tagB` "myextents" 이라는 기존 테이블을 기반으로 하 여 새 범위에 태그를 지정 합니다.

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
새 범위에 대 한 태그를 지정 하는 동안 현재 데이터베이스의 "OldExtents" 테이블에 데이터를 추가 하거나 테이블이 아직 없는 경우 새로 만듭니다 `ingest-by:myTag` . 테이블이 `ingest-by:myTag` "MyExtents" 이라는 기존 테이블을 기반으로 하 여에 태그가 지정 된 범위를 아직 포함 하지 않는 경우에만이 작업을 수행 합니다.

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

를 사용 하 여 새 익스텐트의 태그를 지정 하는 동안 현재 데이터베이스에 있는 "OldExtents" 테이블의 데이터를 바꾸거나 테이블을 만듭니다 (아직 없는 경우) `ingest-by:myTag` .

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

현재 데이터베이스의 "OldExtents" 테이블에 데이터를 추가 하 고 만든 익스텐트 생성 시간을 과거의 특정 datetime으로 설정 합니다.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**반환 출력**
 
또는 명령의 결과로 만들어진 익스텐트의 정보를 반환 합니다 `.set` `.append` .

**예제 출력**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |