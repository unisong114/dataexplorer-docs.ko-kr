---
title: 익스텐트 (데이터 분할) 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 (데이터 분할) 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca66beaad41796dff38a5bd5ce1fad2e0f41fc72
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550506"
---
# <a name="extents-data-shards-management"></a>익스텐트 (데이터 분할) 관리

데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.

## <a name="show-extents"></a>. 익스텐트 표시

**클러스터 수준**

`.show` `cluster` `extents` [`hot`]

클러스터에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다.
`hot`을 지정 하면-핫 캐시에 있어야 하는 익스텐트만 표시 됩니다.

**데이터베이스 수준**

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ExtentId1 `,` *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

지정 된 데이터베이스에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다.
`hot`을 지정 하면-핫 캐시에 있어야 하는 익스텐트만 표시 됩니다.

**테이블 수준**

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ExtentId1 `,` ... *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

`.show``tables` `(` *TableName1* `,` TableName1 ... `,` *TableNameN* `)` `extents`[ `(` *ExtentId1* `,` ExtentId1 `,` ... *Extentidn* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*]]

지정 된 테이블에 있는 익스텐트 (데이터 분할)에 대 한 정보를 표시 합니다. 데이터베이스는 명령의 컨텍스트에서 가져옵니다.
`hot`을 지정 하면-핫 캐시에 있어야 하는 익스텐트만 표시 됩니다.

**참고**

* 데이터베이스 및/또는 테이블 수준 명령을 사용 하면 `| where DatabaseName == '...' and TableName == '...'` 클러스터 수준 명령의 결과를 필터링 (추가) 하는 것 보다 훨씬 빠릅니다.
* 익스텐트 Id의 선택적 목록이 제공 되는 경우 반환 된 데이터 집합은 해당 익스텐트로만 제한 됩니다.
    * 이는 `| where ExtentId in(...)` "완전" 명령의 결과를 필터링 (에 추가) 하는 것 보다 훨씬 빠릅니다.
* `tags`필터를 지정 하는 경우:
  * 반환 된 목록은 태그 컬렉션이 제공 된 *모든* 태그 필터를 따르는 하는 범위로 제한 됩니다.
    * 이는 `| where Tags has '...' and Tags contains '...'` "완전" 명령의 결과를 필터링 (추가) 하는 것 보다 훨씬 빠릅니다.
  * `has`필터는 같음 필터입니다. 지정 된 태그 중 하나로 태그가 지정 되지 않은 익스텐트가 필터링 됩니다.
  * `!has`필터는 같음 부정 필터입니다. 지정 된 태그 중 하나를 사용 하 여 태그가 지정 된 익스텐트가 필터링 됩니다.
  * `contains`필터는 대/소문자를 구분 하지 않는 부분 문자열 필터입니다. 지정 된 문자열이 없는 범위는 해당 태그의 하위 문자열로 필터링 됩니다. 
  * `!contains`필터는 대/소문자를 구분 하지 않는 부분 문자열 부정 필터입니다. 지정 된 문자열이 해당 태그의 하위 문자열로 지정 된 익스텐트는 필터링 됩니다. 
  
  * **예제**
    * `E`테이블의 익스텐트 `T` 는 태그 및 태그로 태그가 지정 됩니다 `aaa` `BBB` `ccc` .
    * 이 쿼리는 다음을 반환 합니다 `E` . 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * 이 쿼리는 *not* 와 `E` 같은 태그로 태그가 지정 되지 않으므로 반환 되지 않습니다 `aa` .
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * 이 쿼리는 다음을 반환 합니다 `E` . 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|출력 매개 변수 |유형 |설명 
|---|---|---
|ExtentId |GUID |익스텐트의 ID 
|DatabaseName |문자열 |범위가 속한 데이터베이스 
|TableName |문자열 |익스텐트가 속하는 테이블 
|MaxCreatedOn |DateTime |익스텐트를 만든 날짜-시간 (병합 된 익스텐트의 경우 원본 익스텐트의 최대 생성 시간) 
|OriginalSize |Double |익스텐트 데이터의 원래 크기 (바이트)입니다. 
|ExtentSize |Double |메모리 내 익스텐트 크기 (압축 된 + 인덱스) 
|CompressedSize |Double |메모리의 익스텐트 데이터 압축 크기 
|IndexSize |Double |익스텐트 데이터의 인덱스 크기 
|블록 |long |익스텐트의 데이터 블록의 양 
|세그먼트 |long |익스텐트의 데이터 세그먼트 양 
|AssignedDataNodes |문자열 | 사용 되지 않음 (빈 문자열)
|LoadedDataNodes |문자열 |사용 되지 않음 (빈 문자열)
|ExtentContainerId |문자열 | 범위가 있는 익스텐트 컨테이너의 ID입니다.
|RowCount |long |익스텐트의 행 크기
|MinCreatedOn |DateTime |범위가 만들어진 날짜-시간 (병합 된 익스텐트의 경우 원본 범위에서 최소 생성 시간) 
|태그|문자열|범위에 대해 정의 된 태그 (있는 경우) 
 
**예제**

```kusto 
// Show volume of extents being created per hour in a specific database
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  

// Show volume of data arriving by table per hour 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart

// Show data size distribution by table 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName

// Show all extents in the database named 'GamesDB' 
.show database GamesDB extents

// Show all extents in the table named 'Games' 
.show table Games extents

// Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2' 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
``` 
 
## <a name="merge-extents"></a>. 범위 병합

**구문**

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

이 명령은 지정 된 테이블에서 해당 Id로 표시 되는 익스텐트 ( [익스텐트 (데이터 분할) 개요](extents-overview.md)참조)를 병합 합니다.

*Merge 작업* 에는 인덱스를 *다시* 작성 하는 두 가지 종류가 있으며 데이터를 완전히 reingests 합니다.

* `async`: 작업이 비동기적으로 수행 됩니다. 결과는 작업 ID (GUID)로,이를 실행 하 여 `.show operations <operation ID>` 명령 상태 & 상태를 볼 수 있습니다.
* `dryrun`: 작업은 병합 되어야 하는 익스텐트만 나열 하지만 실제로 병합 하지는 않습니다. 
* `with(rebuild=true)`: 익스텐트가 다시 작성 됩니다 (데이터가 reingested 됨). 병합 되지 않고 인덱스가 다시 작성 됩니다.

**반환 출력**

출력 매개 변수 |유형 |설명 
---|---|---
OriginalExtentId |문자열 |원본 테이블에서 원본 익스텐트의 원래 범위에 대 한 고유 식별자 (GUID)로, 대상 범위에 병합 되었습니다. 
ResultExtentId |문자열 |원본 범위에서 만들어진 결과 익스텐트의 고유 식별자 (GUID)입니다. 실패 시-"실패" 또는 "중단 됨"입니다.
Duration |timespan |병합 작업을 완료 하는 데 걸린 시간입니다.

**예제**

에서 두 개의 특정 익스텐트 `MyTable` 를 다시 작성 하 여 비동기적으로 수행
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

에서 두 개의 특정 익스텐트 `MyTable` 를 병합 합니다. 동기적으로 수행 됩니다.
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**참고 사항**
- 일반적으로 `.merge` 명령은 거의 실행 되지 않아야 하며,이 명령은 지속적으로의 테이블 및 데이터베이스에 대해 정의 된 [병합 정책](mergepolicy.md) 에 따라 kusto 클러스터의 백그라운드에서 자동으로 수행 됩니다.  
  - 여러 익스텐트를 하나로 병합 하는 기준에 대 한 일반적인 고려 사항은 [병합 정책](mergepolicy.md)에 설명 되어 있습니다.
- `.merge`작업의 최종 상태는 `Abandoned` (작업 ID를 사용 하 여 실행할 때 표시 될 수 있음)입니다 .이 상태는 원본 익스텐트가 원본 `.show operations` 테이블에 더 이상 존재 하지 않기 때문에 원본 익스텐트가 함께 병합 되지 않은 것으로 제안 합니다.
  - 이러한 상태는 다음과 같은 경우에 발생 합니다.
     - 원본 익스텐트가 테이블 보존의 일부로 삭제 되었습니다.
     - 원본 익스텐트가 다른 테이블로 이동 되었습니다.
     - 원본 테이블이 완전히 삭제 되거나 이름이 바뀌었습니다.

## <a name="move-extents"></a>. 익스텐트 이동

**구문**

`.move`[ `async` ] `extents` `all` `from` `table` *Sourcetablename* `to` `table` *destinationtablename*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* `)` ] `from` `table` *Sourcetablename* `to` `table` *Destinationtablename* 

`.move`[ `async` ] `extents` `to` `table` *Destinationtablename*  <|  *쿼리*

이 명령은 특정 데이터베이스의 컨텍스트에서 실행 되며 지정 된 익스텐트를 원본 테이블에서 대상 테이블로 트랜잭션으로 이동 합니다.
원본 및 대상 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

* `async`(선택 사항) 명령이 비동기적으로 실행 되는지 여부를 지정 합니다 .이 경우 작업 ID (Guid)가 반환 되 고 작업의 상태는 [. 작업 표시](operations.md#show-operations) 명령을 사용 하 여 모니터링할 수 있습니다.
    * 이 옵션을 사용 하는 경우 성공적인 실행의 결과를 검색할 수 있습니다 [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령

이동할 익스텐트를 지정 하는 방법에는 다음 세 가지가 있습니다.
1. 특정 테이블의 모든 익스텐트가 이동 됩니다.
2. 원본 테이블에서 익스텐트 Id를 명시적으로 지정 합니다.
3. 결과에서 원본 테이블의 익스텐트 Id를 지정 하는 쿼리를 제공 합니다.

**제한 사항**
- 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다. 
- 원본 테이블의 모든 열은 동일한 이름 및 데이터 형식을 사용 하는 대상 테이블에 있어야 합니다.

**쿼리를 사용 하 여 익스텐트 지정**

```kusto 
.move extents to table TableName <| ...query... 
```

익스텐트는 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다. 

**반환 출력** (동기화 실행의 경우)

출력 매개 변수 |유형 |설명 
---|---|---
OriginalExtentId |문자열 |원본 테이블에서 대상 테이블로 이동 된 원본 범위의 고유 식별자 (GUID)입니다. 
ResultExtentId |문자열 |원본 테이블에서 대상 테이블로 이동한 결과 범위에 대 한 고유 식별자 (GUID)입니다. 오류가 발생 한 경우-"Failed".
세부 정보 |문자열 |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

**예제**

테이블의 모든 익스텐트 `MyTable` 를 테이블로 이동 `MyOtherTable` 합니다.
```kusto
.move extents all from table MyTable to table MyOtherTable
```

익스텐트 Id로 2 개의 특정 익스텐트를 테이블에서 테이블로 이동 `MyTable` `MyOtherTable` 합니다.
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

모든 익스텐트를 두 개의 특정 테이블 ( `MyTable1` , `MyTable2` )에서 테이블로 이동 `MyOtherTable` 합니다.
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**예제 출력** 

|OriginalExtentId |ResultExtentId| 세부 정보
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>. 범위 삭제

지정 된 데이터베이스/테이블에서 익스텐트를 삭제 합니다. 이 명령에는 여러 가지 변형이 있습니다. 즉, 하나의 변형에서 삭제할 익스텐트가 Kusto 쿼리로 지정 되는 반면 다른 변형 범위는 아래에 설명 된 미니 언어를 사용 하 여 지정 됩니다. 
 
### <a name="specifying-extents-with-a-query"></a>쿼리를 사용 하 여 익스텐트 지정

제공 된 쿼리에 의해 반환 되는 익스텐트가 있는 테이블의 [테이블 관리자 권한](../management/access-control/role-based-authorization.md) foreach가 필요 합니다.

익스텐트를 삭제 하거나를 사용 하는 경우 실제로 삭제 하지 않고 해당 항목만 보고 합니다 `whatif` .

**구문**

`.drop``extents`[ `whatif` ] <| *쿼리*

익스텐트는 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다. 
 
### <a name="dropping-a-specific-extent"></a>특정 익스텐트 삭제

테이블 이름이 지정 된 경우에는 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름이 지정 되지 않은 경우에는 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

**구문**

`.drop``extent` *ExtentId* [ `from` *TableName*]

### <a name="dropping-specific-multiple-extents"></a>특정 다중 익스텐트 삭제

테이블 이름이 지정 된 경우에는 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름이 지정 되지 않은 경우에는 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

**구문**

`.drop``extents` `(` *ExtentId1* `,` ... *Extentidn* `)` [ `from` *TableName*]

### <a name="specifying-extents-by-properties"></a>속성에 따라 익스텐트 지정

테이블 이름이 지정 된 경우에는 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름이 지정 되지 않은 경우에는 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *개수 개수*]

* `older`: *N* 일/시간 보다 오래 된 익스텐트만 삭제 됩니다. 
* `trim`: 익스텐트의 합계가 원하는 크기 (MaxSize)와 일치할 때까지 작업에서 데이터베이스의 데이터를 자릅니다. 
* `limit`: 작업은 첫 번째 한도 *수* 범위에 적용 됩니다. 

명령은 `.drop-pretend` `.drop` 명령이 실행 되었지만 실제로 실행 되지 않는 것 처럼 출력을 생성 하는 에뮬레이션 (대신) 모드를 지원 합니다.

**예제**

데이터베이스의 모든 테이블에서 10 일 넘게 만들어진 모든 익스텐트 제거`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

테이블의 모든 익스텐트를 제거 합니다 `Table1` `Table2` . 생성 시간은 10 일 전입니다.

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

에뮬레이션 모드: 명령에 의해 제거 되는 익스텐트를 표시 합니다 (익스텐트 ID 매개 변수는이 명령에 적용할 수 없음).

```kusto
.drop-pretend extents older 10 days from all tables
```

' TestTable '에서 모든 익스텐트를 제거 합니다.

```kusto
.drop extents from TestTable
```
 
**반환 출력**

|출력 매개 변수 |유형 |설명 
|---|---|---
|ExtentId |문자열 |명령의 결과로 삭제 된 ExtentId 
|TableName |문자열 |범위가 속한 테이블 이름  
|Adventureworks.createdon |DateTime |범위가 처음 생성 된 시간에 대 한 정보를 포함 하는 타임 스탬프 
 
**예제 출력** 

|익스텐트 Id |테이블 이름 |만든 날짜 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>. 익스텐트 바꾸기

**구문**

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* `<| 
{` *query for extents to be dropped from table* `},{` *테이블에 이동할 익스텐트* 를 테이블 쿼리에서 삭제할 익스텐트를 쿼리 합니다.`}`

이 명령은 특정 데이터베이스의 컨텍스트에서 실행 되며 지정 된 익스텐트를 원본 테이블에서 대상 테이블로 이동 하 고 대상 테이블에서 지정 된 범위를 삭제 합니다.
모든 drop 및 move 작업은 단일 트랜잭션에서 수행 됩니다.

원본 및 대상 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

* `async`(선택 사항) 명령이 비동기적으로 실행 되는지 여부를 지정 합니다 .이 경우 작업 ID (Guid)가 반환 되 고 작업의 상태는 [. 작업 표시](operations.md#show-operations) 명령을 사용 하 여 모니터링할 수 있습니다.
    * 이 옵션을 사용 하는 경우 성공적인 실행의 결과를 검색할 수 있습니다 [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령

두 개의 쿼리를 제공 하 여 삭제 하거나 이동할 익스텐트를 지정 합니다.
- *테이블에서 삭제할 익스텐트 쿼리* -이 쿼리의 결과는 익스텐트 id를 지정 합니다.  
대상 테이블에서 삭제 해야 합니다.
- *테이블에 이동할 익스텐트 쿼리* -이 쿼리의 결과는 대상 테이블로 이동 해야 하는 원본 테이블의 익스텐트 id를 지정 합니다.

두 쿼리 모두 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 해야 합니다.

**제한 사항**
- 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다. 
- *테이블에서 익스텐트를 삭제* 하기 위해 쿼리에서 지정 된 모든 익스텐트는 대상 테이블에 속해야 합니다.
- 원본 테이블의 모든 열은 동일한 이름 및 데이터 형식을 사용 하는 대상 테이블에 있어야 합니다.

**반환 출력** (동기화 실행의 경우)

출력 매개 변수 |유형 |설명 
---|---|---
OriginalExtentId |문자열 |원본 테이블에서 대상 테이블로 이동 된 원본 범위의 고유 식별자 (GUID) 또는 삭제 된 대상 테이블의 범위입니다 (예를 들어,
ResultExtentId |문자열 |원본 테이블에서 대상 테이블로 이동 된 결과 범위의 고유 식별자 (GUID) 이거나, 대상 테이블에서 익스텐트를 삭제 한 경우에는-empty입니다. 오류가 발생 한 경우-"Failed".
세부 정보 |문자열 |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

> [!NOTE]
> *테이블 쿼리에서 삭제할 익스텐트가* 반환한 익스텐트가 대상 테이블에 없는 경우 명령이 실패 합니다. 이는 대체 명령이 실행 되기 전에 익스텐트가 병합 된 경우에 발생할 수 있습니다. 누락 된 익스텐트의 명령이 실패 하는지 확인 하려면 쿼리가 필요한 ExtentIds를 반환 하는지 확인 합니다. 삭제할 익스텐트가 테이블 MyOtherTable에 없는 경우 아래 예 #1 실패 합니다. 그러나 drop에 대 한 쿼리가 익스텐트 id를 반환 하지 않았으므로 drop이 존재 하지 않는 경우에도 #2 예제가 성공 합니다. 

**예제**

다음 명령을 사용 하 여 두 개의 특정 테이블 (,)의 모든 익스텐트를 `MyTable1` `MyTable2` 테이블로 이동 `MyOtherTable` 하 고 `MyOtherTable` 태그가 지정 된의 모든 익스텐트를 삭제 합니다 `drop-by:MyTag` .

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

**예제 출력** 

|OriginalExtentId |ResultExtentId |세부 정보
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 


다음 명령은 특정 테이블 ()에서 테이블로 모든 익스텐트를 이동 `MyTable1` `MyOtherTable` 하 고 `MyOtherTable` 해당 ID로의 특정 익스텐트를 삭제 합니다.


```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

다음 명령은 idempotent 논리를 구현 하므로 테이블 `t_dest` 에서 테이블로 이동할 익스텐트가 있는 경우에만 테이블에서 익스텐트를 삭제 합니다 `t_source` `t_dest` .

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```

## <a name="drop-extent-tags"></a>. 익스텐트 태그를 삭제 합니다.

**구문**

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *쿼리*

* `async`(선택 사항) 명령이 비동기적으로 실행 되는지 여부를 지정 합니다 .이 경우 작업 ID (Guid)가 반환 되 고 작업의 상태는 [. 작업 표시](operations.md#show-operations) 명령을 사용 하 여 모니터링할 수 있습니다.
    * 이 옵션을 사용 하는 경우 성공적인 실행의 결과를 검색할 수 있습니다 [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령

명령은 특정 데이터베이스의 컨텍스트에서 실행 되 고 제공 된 [익스텐트 태그](extents-overview.md#extent-tagging) 를 제공 된 데이터베이스 및 테이블의 모든 범위에서 삭제 합니다. 여기에는 태그가 포함 됩니다.  

다음 두 가지 방법으로 익스텐트를 제거할 태그를 지정할 수 있습니다.

1. 지정 된 테이블의 모든 범위에서 제거 해야 하는 태그를 명시적으로 지정 합니다.
2. 결과에 테이블의 익스텐트 Id를 지정 하는 쿼리 및 foreach 익스텐트의 (제거 해야 하는 태그)를 제공 합니다.

**제한 사항**
- 모든 익스텐트는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다. 

**쿼리를 사용 하 여 익스텐트 지정**

관련 된 모든 원본 및 대상 테이블에 대해 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

```kusto 
.drop extent tags <| ...query... 
```

삭제할 범위 및 태그는 "ExtentId" 열과 "Tags" 라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다. 

*참고*: [kusto .net 클라이언트 라이브러리](../api/netfx/about-kusto-data.md)를 사용 하는 경우 원하는 명령을 생성 하기 위해 다음 메서드를 사용할 수 있습니다.
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**반환 출력**

출력 매개 변수 |유형 |설명 
---|---|---
OriginalExtentId |문자열 |태그가 수정 되었으며 작업의 일부로 삭제 된 원래 익스텐트의 고유 식별자 (GUID)입니다. 
ResultExtentId |문자열 |수정 된 태그를 포함 하 고 작업의 일부로 생성 및 추가 되는 결과 범위의 고유 식별자 (GUID)입니다. 오류가 발생 한 경우-"Failed".
ResultExtentTags |문자열 |결과 익스텐트의 태그가 지정 된 태그 컬렉션 (있는 경우) 또는 작업이 실패 하는 경우 "null"입니다.
세부 정보 |문자열 |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

**예제**

태그가 `drop-by:Partition000` 지정 된 테이블의 모든 범위에서 태그를 삭제 `MyOtherTable` 합니다.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

`drop-by:20160810104500` `a random tag` 테이블의 모든 범위에서, 및/또는 태그 중 `drop-by:20160810` 하나로 태그가 지정 된 범위를 삭제 `My Table` 합니다.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

`drop-by`테이블의 익스텐트의 모든 태그를 삭제 `MyTable` 합니다.

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

테이블의 익스텐트에서 regex와 일치 하는 모든 태그 `drop-by:StreamCreationTime_20160915(\d{6})` 를 삭제 `MyTable` 합니다.

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**예제 출력** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 세부 정보
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>. 익스텐트 태그 변경

**구문**

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TagN*'] `)`  <|  *쿼리*

명령은 특정 데이터베이스의 컨텍스트에서 실행 되며 지정 된 쿼리에서 반환 되는 모든 익스텐트의 [익스텐트 태그](extents-overview.md#extent-tagging) 를 제공 된 태그 집합으로 변경 합니다.

변경할 범위 및 태그는 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다.

* `async`(선택 사항) 명령이 비동기적으로 실행 되는지 여부를 지정 합니다 .이 경우 작업 ID (Guid)가 반환 되 고 작업의 상태는 [. 작업 표시](operations.md#show-operations) 명령을 사용 하 여 모니터링할 수 있습니다.
    * 이 옵션을 사용 하는 경우 성공적인 실행의 결과를 검색할 수 있습니다 [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령

관련 된 모든 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

**제한 사항**
- 모든 익스텐트는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다. 

**반환 출력**

출력 매개 변수 |유형 |설명 
---|---|---
OriginalExtentId |문자열 |태그가 수정 되었으며 작업의 일부로 삭제 된 원래 익스텐트의 고유 식별자 (GUID)입니다. 
ResultExtentId |문자열 |수정 된 태그를 포함 하 고 작업의 일부로 생성 및 추가 되는 결과 범위의 고유 식별자 (GUID)입니다. 오류가 발생 한 경우-"Failed".
ResultExtentTags |문자열 |결과 익스텐트가 태그가 지정 된 태그 컬렉션 이거나, 작업이 실패 하는 경우에는 "null"입니다.
세부 정보 |문자열 |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

**예제**

테이블에 있는 모든 익스텐트의 태그 `MyTable` 를로 변경 `MyTag` 합니다.

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

테이블에서 `MyTable` 및로 태그가 지정 된 모든 익스텐트의 태그를 `drop-by:MyTag` 변경 `drop-by:MyNewTag` `MyOtherNewTag` 합니다.

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**예제 출력** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 세부 정보
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | drop by: MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | drop by: MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | drop by: MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | drop by: MyNewTag MyOtherNewTag| 

