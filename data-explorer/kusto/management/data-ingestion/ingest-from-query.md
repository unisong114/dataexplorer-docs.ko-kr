---
title: 쿼리에서 수집(.set, .aend, .set-or-append, .set-or-replace) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리(.set, .append, .set-or-append, .set-or-replace)에서 인제스트에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: fb0bbb06bb8d28dd3951a7faedf55b0d84155301
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521457"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>쿼리에서 인제스트(.set, .aend, .set-or-append, .set-or-replace)

이러한 명령은 쿼리 또는 컨트롤 명령을 실행하고 쿼리 결과를 테이블로 인제합니다. 이러한 명령의 차이점은 기존 테이블과 존재하지 않는 테이블 및 데이터를 처리하는 방법입니다.

|명령          |테이블이 있는 경우                     |테이블이 없는 경우                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |명령이 실패합니다.                  |테이블이 만들어지고 데이터가 수집됩니다.|
|`.append`        |데이터가 테이블에 추가됩니다.      |명령이 실패합니다.                        |
|`.set-or-append` |데이터가 테이블에 추가됩니다.      |테이블이 만들어지고 데이터가 수집됩니다.|
|`.set-or-replace`|데이터는 테이블의 데이터를 대체합니다.|테이블이 만들어지고 데이터가 수집됩니다.|

**구문**

`.set`[`async`] 테이블`with` `(` *이름* [`,` *속성 이름* `=` 속성 *값* [...] `)`] `<|` *쿼리Or 명령*

`.append`[`async`] 테이블`with` `(` *이름* [`,` *속성 이름* `=` *속성값* [ ... `])`] `<|` *쿼리Or 명령*

`.set-or-append`[`async`] 테이블`with` `(` *이름* [`,` *속성 이름* `=` 속성 *값* [...] `)`] `<|` *쿼리Or 명령*

`.set-or-replace`[`async`] 테이블`with` `(` *이름* [`,` *속성 이름* `=` 속성 *값* [...] `)`] `<|` *쿼리Or 명령*

**인수**

* `async`: 지정하면 명령이 즉시 반환되고 백그라운드에서 계속 수행됩니다. 명령의 결과에는 명령과 `OperationId` 함께 `.show operation` 사용하여 처리 완료 상태 및 결과를 검색할 수 있는 값이 포함됩니다.
* *테이블 이름*: 데이터를 수집할 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스를 기준으로 합니다.
* *속성 이름*, *PropertyValue*: 인기 프로세스에 영향을 주는 모든 수의 인기 속성입니다.

 지원되는 인비온 속성:

|속성        |Description|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 수집된 데이터 익스텐트 생성 시 사용할 날짜 시간 값(ISO8601 문자열로 서식이 지정)입니다. 지정되지 않은 경우 현재 값(now())이 사용됩니다.|
|`extend_schema`  | 지정된 경우 테이블의 스키마를 확장하도록 명령하는 부울 값입니다(기본값은 false). 이 옵션은 .append .set-or-append 및 설정 또는 바꾸기 명령에만 적용됩니다. 유일하게 허용되는 스키마 확장에는 테이블의 끝에 추가되는 추가 열이 있습니다.|
|`recreate_schema`  | 지정된 경우 명령이 테이블의 스키마를 다시 만들 수 있는지 여부를 설명하는 부울 값입니다(기본값은 false). 이 옵션은 설정 또는 바꾸기 명령에만 적용됩니다. 이 옵션은 둘 다 설정된 경우 extend_schema 속성보다 우선합니다.|
|`folder`         | 테이블에 할당할 폴더입니다. 테이블이 이미 있는 경우 이 속성은 테이블의 폴더를 재정의합니다.|
|`ingestIfNotExists`   | 지정된 경우 테이블에 인제스트바이(ingest-by: 동일한 값)로 태그가 지정된 데이터가 이미 있는 경우 수집이 성공하지 못하도록 하는 문자열 값입니다.|
|`policy_ingestiontime`   | 지정하면 이 명령으로 만든 테이블에서 [수집 시간 정책](../../management/ingestiontime-policy.md)을 사용하도록 설정할지 여부를 설명하는 부울 값입니다. 기본값은 true입니다.|
|`tags`   | 수집 중에 실행할 유효성 검사를 나타내는 JSON 문자열입니다.|
|`docstring`   | 테이블을 문서화하는 문자열입니다.|

  또한 명령 자체의 동작을 제어하는 속성이 있습니다.

|속성        |Type    |Description|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |명령이 쿼리를 병렬로 실행하는 모든 노드에서 인더스트임을 나타냅니다. (기본값 `false`.)  아래 발언을 참조하십시오.|

* *QueryOrCommand*: 쿼리 또는 제어 명령의 텍스트로, 그 결과는 수집할 데이터로 사용됩니다.

> [!NOTE]
> 제어 `.show` 명령만 지원됩니다.

**주의**

* `.set-or-replace`테이블이 있는 경우(기존 데이터 샤드 삭제) 테이블의 데이터를 대체하거나, 아직 존재하지 않는 경우 대상 테이블을 만듭니다.
  테이블 스키마 는 `extend_schema` 중 하나 또는 `recreate_schema` 들여레이션 속성 중 `true`하나가 설정되지 않으면 유지됩니다. 스키마가 수정된 경우 자체 트랜잭션에서 실제 데이터를 수집하기 전에 이 오류가 발생하므로 데이터를 수집하지 못했다고 해서 스키마가 수정되지 않았다는 의미는 아닙니다.
* `.set-or-append`및 `.append` 명령은 섭취 속성이 로 `extend_schema` 설정되지 않는 한 `true`스키마를 유지합니다. 스키마가 수정된 경우 자체 트랜잭션에서 실제 데이터를 수집하기 전에 이 오류가 발생하므로 데이터를 수집하지 못했다고 해서 스키마가 수정되지 않았다는 의미는 아닙니다.
* 수집 작업당 1GB 미만으로 제한하는 **것이 좋습니다.** 필요한 경우 여러 개의 인베이션 명령을 사용할 수 있습니다.
* 데이터 수집은 쿼리 실행을 포함하여 클러스터의 동시 활동에 영향을 줄 수 있는 리소스 집약적인 작업입니다. 이러한 명령을 동시에 함께 실행하지 마십시오.
* 결과 집합 스키마를 대상 테이블의 스키마와 일치시키는 경우 비교는 열 형식을 기반으로 합니다. 열 이름이 일치하지 않으므로 쿼리 결과 스키마 열이 테이블과 동일한 순서로 되어 있는지 확인하고 그렇지 않으면 데이터가 잘못된 열로 수집됩니다.
* 플래그를 `distributed` `true` 설정하면 쿼리에서 생성되는 데이터의 양이 크고(1GB의 데이터 **초과)** 쿼리에 직렬화가 필요하지 않은 경우(여러 노드가 병렬로 출력을 생성할 수 있음)가 유용합니다.
  쿼리 결과가 작으면 불필요하게 많은 작은 데이터 샤드를 생성할 수 있으므로 이 플래그를 사용하지 않는 것이 좋습니다.

**예** 

"LogsTable"과 동일한 스키마를 가지며 마지막 시간의 모든 오류 레코드를 보유하는 현재 데이터베이스에서 "RecentErrors"라는 새 테이블을 만듭니다.

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

"MyExtents"라는 기존 테이블을 기반으로 30일 전에 만들어진 데이터베이스의 모든 익스텐션의 ID를 보유하는 현재 데이터베이스에서 "OldExtents"라는 새 테이블을 만듭니다.

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

단일 열("ExtentId")이 있고 30일 전에 생성된 데이터베이스의 모든 익스텐트의 범위 ID를 보유한 현재 데이터베이스의 "OldExtents"라는 기존 테이블에 데이터를 추가하면서 새 익스텐션에 태그를 `tagA` 지정하고 `tagB`"MyExtents"라는 기존 테이블을 기반으로 합니다.

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
현재 데이터베이스의 "OldExtents" 테이블에 데이터를 추가하거나 `ingest-by:myTag`에 새 익스텐션에 태그를 지정하는 동안 테이블을 만들 수 있습니다. 테이블에 "MyExtents"라는 기존 테이블을 기반으로 `ingest-by:myTag`에 태그가 지정된 익스텐트가 아직 포함되어 있지 않은 경우에만 이렇게 합니다.

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

현재 데이터베이스의 "OldExtents" 테이블의 데이터를 바꾸거나 아직 존재하지 않는 테이블을 만들면서 새 익스텐션에 `ingest-by:myTag`태그를 지정합니다.

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

생성된 익스텐트 생성 시간을 과거의 특정 날짜 시간으로 설정하는 동안 현재 데이터베이스의 "OldExtents" 테이블에 데이터를 추가했다.

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**출력 반환**
 
`.set` 또는 명령의 `.append` 결과로 생성된 익스텐트에 대한 정보를 반환합니다.

**예제 출력**

|익스텐트 ID |오리지널 사이즈 |익스텐트 크기 |압축 크기 |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |