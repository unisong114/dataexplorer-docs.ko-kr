---
title: 익스텐트(데이터 샤드) 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트(데이터 샤드) 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e94b830809bf079e612b477292d00d2dbe85e60e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744734"
---
# <a name="extents-data-shards-management"></a>익스텐트(데이터 샤드) 관리

데이터 샤드는 Kusto의 **익스텐트라고** 하며 모든 명령은 동의어로 "익스텐트" 또는 "익스텐츠"를 사용합니다.

## <a name="show-extents"></a>.show 익스텐츠

**클러스터 수준**

`.show` `cluster` `extents` [`hot`]

클러스터에 있는 익스텐트(데이터 샤드)에 대한 정보를 표시합니다.
지정된 `hot` 경우 - 핫 캐시에 있을 것으로 예상되는 익스텐트만 표시합니다.

**데이터베이스 수준**

`.show``database` *데이터베이스* `extents` 이름`(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`[ [ [`has`|`contains`[|`!has`|`!contains` *태그1* [ (`and` `tags` 2 ) *태그2*...]]

지정된 데이터베이스에 있는 익스텐트(데이터 샤드)에 대한 정보를 표시합니다.
지정된 경우 `hot` - 핫 캐시에 있을 것으로 예상되는 익스텐트만 표시합니다.

**테이블 수준**

`.show``table` *테이블* `extents` 이름`(`[*ExtentId1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`[ [ [`has`|`contains`[|`!has`|`!contains` *태그1* [ (`and` `tags` 2 ) *태그2*...]]

`.show``tables` `,`테이블네임1 ... *TableName1* `(` `,` *테이블네임* `)` `extents` `(`[*익스텐트Id1*`,`... `,` *ExtentIdN*`)`]`hot`[`where` `tags` `has`|`contains`|`!has`|`!contains`[ [ [`has`|`contains`[|`!has`|`!contains` *태그1* [ (`and` `tags` 2 ) *태그2*...]]

지정된 테이블에 있는 익스텐트(데이터 샤드)에 대한 정보를 표시합니다(데이터베이스는 명령의 컨텍스트에서 가져옵니다).
지정된 경우 `hot` - 핫 캐시에 있을 것으로 예상되는 익스텐트만 표시합니다.

**참고**

* 데이터베이스 및/또는 테이블 수준 명령을 사용하는 것이 클러스터 `| where DatabaseName == '...' and TableName == '...'`수준 명령의 결과를 필터링(추가)하는 것보다 훨씬 빠릅니다.
* 선택적 익스텐트 아이디 목록이 제공되면 반환된 데이터 집합은 해당 범위로만 제한됩니다.
    * 다시 말하지만, 이것은 "베어"명령의 결과를 필터링 (에 추가)하는 `| where ExtentId in(...)` 것보다 훨씬 빠릅니다.
* 필터가 `tags` 지정된 경우:
  * 반환된 목록은 태그 컬렉션이 제공된 *모든* 태그 필터를 준수하는 익스텐션으로 제한됩니다.
    * 다시 말하지만, 이것은 필터링 (추가) `| where Tags has '...' and Tags contains '...'`"베어"명령의 결과보다 훨씬 빠릅니다.
  * `has`필터는 같음 필터입니다: 지정된 태그 중 하나에 태그가 지정되지 않은 익스텐트는 필터링됩니다.
  * `!has`필터는 같음 네거티브 필터입니다: 지정된 태그 중 하나에 태그가 지정된 익스텐트는 필터링됩니다.
  * `contains`필터는 대/소문자를 구분하지 않는 하위 문자열 필터입니다. 
  * `!contains`필터는 대/소문자를 구분하지 않는 하위 문자열 네거티브 필터입니다. 
  
  * **예**
    * 테이블의 `E` `T` 익스텐트는 태그 `aaa` `BBB` 및 `ccc`및 로 태그가 지정됩니다.
    * 이 쿼리는 `E`반환됩니다. 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * 이 쿼리는 `E` `aa` *반환되지* 않습니다(태그가 같음)으로 태그가 지정되지 않았습니다.
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * 이 쿼리는 `E`반환됩니다. 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|출력 매개 변수 |Type |Description 
|---|---|---
|익스텐트 ID |Guid |범위의 ID 
|DatabaseName |String |해당 범위가 속한 데이터베이스 
|TableName |String |해당 익스텐스가 속한 표 
|맥스생성온 |DateTime |익스텐션이 생성된 날짜 시간(병합된 익스텐트의 경우 - 소스 익스텐트 간의 생성 시간 최대) 
|오리지널 사이즈 |Double |익스텐트 데이터의 바이트별 원본 크기 
|익스텐트 크기 |Double |메모리 범위 크기(압축 + 인덱스) 
|압축 크기 |Double |메모리에 있는 익스텐트 데이터의 압축 크기 
|IndexSize |Double |익스텐트 데이터의 인덱스 크기 
|블록 |long |데이터 블록의 양 
|세그먼트 |long |범위내의 데이터 세그먼트 양 
|할당된 데이터 노드 |String | 더 이상 사용되지 않습니다(빈 문자열)
|로드된 데이터 노드 |String |더 이상 사용되지 않습니다(빈 문자열)
|익스텐트컨테이너ID |String | 익스텐트 컨테이너의 ID
|RowCount |long |익스텐스 내 행 수
|민비드온 |DateTime |익스텐션이 생성된 날짜 시간(병합된 익스텐트의 경우 - 소스 익스텐트 간의 생성 시간 최소) 
|태그들|String|익스텐트에 대해 정의된 태그(있는 경우) 
 
**예**

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
 
## <a name="merge-extents"></a>.merge 익스텐트

**구문**

`.merge``[async | dryrun]` *테이블 네임* `(` `,` *GUID1* [ *GUID2* ...] `)``[with(rebuild=true)]`

이 명령은 지정된 테이블에 해당 아이디로 표시된 익스텐트(참조: [범위(데이터 샤드) 개요)를](extents-overview.md)병합합니다.

병합 작업에는 병합(인덱스를 *Merge* 다시 빌드) 및 *다시 작성(데이터를* 완전히 다시 조정)의 2가지 맛이 있습니다.

* `async`: 작업이 비동기적으로 수행됩니다 - 결과는 명령의 상태를 & 상태를 보기 위해 `.show operations <operation ID>` 실행할 수 있는 GUID(작업 ID)가 됩니다.
* `dryrun`: 작업에는 병합해야 하는 익스텐트만 나열되지만 실제로병합되지는 않습니다. 
* `with(rebuild=true)`: 익스텐스가 병합되는 대신(데이터가 다시 빌드됩니다) 익스텐트(인덱스가 다시 빌드됩니다).

**출력 반환**

출력 매개 변수 |Type |Description 
---|---|---
오리지널익시드 |문자열 |대상 익스텐트에 병합된 원본 테이블의 원래 익스텐트에 대한 고유 식별자(GUID)입니다. 
결과익ID |문자열 |소스 익스텐트에서 생성된 결과 익스텐디에 대한 고유 식별자(GUID)입니다. 실패 시 - "실패" 또는 "포기".
Duration |timespan |병합 작업을 완료하는 데 걸린 기간입니다.

**예**

에서 `MyTable`두 개의 특정 익스텐을 다시 빌드합니다.
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

에서 두 개의 특정 `MyTable`익스텐을 병합합니다.
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**참고 사항**
- 일반적으로 `.merge` 명령은 수동으로 실행되는 경우는 거의 없으며 Kusto 클러스터의 백그라운드에서 자동으로 수행됩니다(명령의 테이블 및 데이터베이스에 대해 정의된 [병합 정책에](mergepolicy.md) 따라).  
  - 여러 익스텐을 단일 익스텐션으로 병합하기 위한 기준에 대한 일반적인 고려 사항은 [병합 정책에서](mergepolicy.md)설명합니다.
- `.merge`작업의 가능한 최종 `Abandoned` 상태 (작업 ID로 `.show operations` 실행할 때 볼 수 있습니다)-이 상태는 소스 익스텐트 중 일부가 더 이상 원본 테이블에 존재하지 않기 때문에 소스 익스텐스가 함께 병합되지 않음을 시사합니다.
  - 이러한 상태는 다음과 같은 경우에 발생할 것으로 예상됩니다.
     - 소스 익스텐션이 테이블 보존의 일부로 삭제되었습니다.
     - 소스 익스텐스가 다른 테이블로 이동되었습니다.
     - 원본 테이블이 완전히 삭제되었거나 이름이 바뀌었습니다.

## <a name="move-extents"></a>.move 익스텐트

**구문**

`.move`[`async` `extents` `all` `from` ] `table` *소스테이블이름* `to` `table` *대상테이블이름*

`.move`[`async` `extents` `(` ] *GUID1* [`,` *GUID2* ...] `)` `table` *DestinationTableName* *SourceTableName* 소스테이블이름 `to` 대상테이블이름 `from` `table` 

`.move`[`async` `extents` `to` `table` ] *대상테이블 이름* <| *쿼리*

이 명령은 특정 데이터베이스의 컨텍스트에서 실행되며 지정된 익스텐션을 원본 테이블에서 대상 테이블로 트랜잭션으로 이동합니다.
원본 및 대상 테이블에 대한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

* `async`(선택 사항)은 명령이 비동기적으로 실행되는지 여부를 지정합니다(이 경우 작업 ID(Guid)가 반환되고 [.show 작업](operations.md#show-operations) 명령을 사용하여 작업의 상태를 모니터링할 수 있습니다).
    * 이 옵션을 사용하는 경우 성공적인 실행 결과를 [.show 작업 세부 정보](operations.md#show-operation-details) 명령)을 검색할 수 있습니다.

이동할 범위를 지정하는 방법에는 세 가지가 있습니다.
1. 특정 테이블의 모든 익스텐을 이동해야 합니다.
2. 소스 테이블의 익스텐트 아이디를 명시적으로 지정합니다.
3. 결과가 소스 테이블의 범위 아이디를 지정하는 쿼리를 제공합니다.

**제한 사항**
- 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다. 
- 원본 테이블의 모든 열은 이름과 데이터 형식이 같은 대상 테이블에 존재할 것으로 예상됩니다.

**쿼리를 통해 익스텐트 지정**

```kusto 
.move extents to table TableName <| ...query... 
```

익스텐드는 "ExtentId"라는 열로 레코드 집합을 반환하는 Kusto 쿼리를 사용하여 지정됩니다. 

**출력 반환(동기화** 실행용)

출력 매개 변수 |Type |Description 
---|---|---
오리지널익시드 |문자열 |대상 테이블로 이동된 원본 테이블의 원래 익스텐트에 대한 고유 식별자(GUID)입니다. 
결과익ID |문자열 |원본 테이블에서 대상 테이블로 이동된 결과 익스텐션에 대한 고유 식별자(GUID)입니다. 실패 시 - "실패".
세부 정보 |문자열 |작업이 실패할 경우 실패 세부 정보를 포함합니다.

**예**

테이블의 `MyTable` 모든 익스텐을 `MyOtherTable`테이블로 이동합니다.
```kusto
.move extents all from table MyTable to table MyOtherTable
```

테이블에서 `MyTable` 테이블로 `MyOtherTable`2개의 특정 익스텐디를 이동합니다.
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

모든 익스텐스를 2개의`MyTable1`특정 `MyTable2`테이블 `MyOtherTable`(, ) 에서 테이블로 이동합니다.
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**예제 출력** 

|오리지널익시드 |결과익ID| 세부 정보
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>.drop 익스텐트

지정된 데이터베이스 / 테이블에서 익스텐을 삭제합니다. 이 명령에는 여러 변형이 있습니다: 한 변형에서 삭제할 익스텐트는 Kusto 쿼리에 의해 지정되는 반면, 다른 변형 익스텐트는 아래에 설명된 미니 언어를 사용하여 지정됩니다. 
 
### <a name="specifying-extents-with-a-query"></a>쿼리를 통해 익스텐트 지정

제공된 쿼리에서 반환되는 익스텐션이 있는 테이블의 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

익스텐드 익스텐트(또는 사용 `whatif` 시 실제로 삭제하지 않고 보고).

**구문**

`.drop``extents` [`whatif`] <| *쿼리*

익스텐드는 "ExtentId"라는 열로 레코드 집합을 반환하는 Kusto 쿼리를 사용하여 지정됩니다. 
 
### <a name="dropping-a-specific-extent"></a>특정 범위 삭제

테이블 이름이 지정된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

테이블 이름이 지정되지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

**구문**

`.drop``extent` *익스텐트Id* [`from` *테이블이름*]

### <a name="dropping-specific-multiple-extents"></a>특정 다중 익스텐트 삭제

테이블 이름이 지정된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

테이블 이름이 지정되지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

**구문**

`.drop``extents` `,`익스텐트Id1 ... *ExtentId1* `(` *익스텐트IdN* `)` [`from` *테이블이름*]

### <a name="specifying-extents-by-properties"></a>속성별 익스텐트 지정

테이블 이름이 지정된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

테이블 이름이 지정되지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

`.drop``extents` [`older` *N* ()`days` | `hours`) ( `from` ( (`MB` | `GB` | `bytes`(`limit` 표*이름*  |  `all` `tables`) [ (`trim` `by` )`extentsize` | `datasize` *N* ) [ [ *제한 카운트*]

* `older`: 1일/시간보다 오래된 범위만 삭제됩니다. *N* 
* `trim`: 익스텐스의 합계가 원하는 크기와 일치할 때까지 데이터베이스의 데이터를 트리밍합니다(MaxSize) 
* `limit`: 첫 번째 *LimitCount* 익스텐트에 작업이 적용됩니다. 

명령은`.drop-pretend` `.drop`에뮬레이션(대신) 모드를 지원하며, 이 모드는 명령이 실행된 것처럼 출력을 생성하지만 실제로 실행하지는 않습니다.

**예**

데이터베이스의 모든 테이블에서 10일 이상 전에 만든 모든 익스텐스 제거`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

테이블의 `Table1` 모든 익스텐션을 `Table2`제거하고 생성 시간이 10일 이상 지난 다음을 수행합니다.

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

에뮬레이션 모드: 명령에 의해 제거될 익스텐디를 표시합니다(익스텐트 ID 매개 변수는 이 명령에 적용되지 않음).

```kusto
.drop-pretend extents older 10 days from all tables
```

'TestTable'에서 모든 익스텐스를 제거합니다.

```kusto
.drop extents from TestTable
```
 
**출력 반환**

|출력 매개 변수 |Type |Description 
|---|---|---
|익스텐트 ID |String |명령의 결과로 삭제된 ExtentId 
|TableName |String |표 이름( 익스텐이 속한 위치)  
|생성 된 온 |DateTime |익스텐션이 처음 생성된 시기에 대한 정보를 보유하는 타임스탬프 
 
**예제 출력** 

|익스텐트 ID |테이블 이름 |만든 날짜 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>.바꾸기 익스텐트

**구문**

`.replace`[`async` `extents` `in` `table` [ *DestinationTableName* `<| 
{` *익스텐션을 테이블로 이동하기 위한 익스텐션에*`},{`대해 테이블*쿼리에서 삭제할 범위 쿼리*`}`

이 명령은 특정 데이터베이스의 컨텍스트에서 실행되고, 지정된 익스텐션을 원본 테이블에서 대상 테이블로 이동하고, 대상 테이블에서 지정된 익스텐션을 삭제합니다.
모든 드롭 앤 이동 작업은 단일 트랜잭션에서 수행됩니다.

원본 및 대상 테이블에 대한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

* `async`(선택 사항)은 명령이 비동기적으로 실행되는지 여부를 지정합니다(이 경우 작업 ID(Guid)가 반환되고 [.show 작업](operations.md#show-operations) 명령을 사용하여 작업의 상태를 모니터링할 수 있습니다).
    * 이 옵션을 사용하는 경우 성공적인 실행 결과를 [.show 작업 세부 정보](operations.md#show-operation-details) 명령)을 검색할 수 있습니다.

삭제하거나 이동해야 하는 익스텐스를 지정하는 작업은 2개의 쿼리를 제공하여 수행됩니다.
- *테이블에서 삭제할 익스텐디에 대한 쿼리* - 이 쿼리의 결과는 범위 ID를 지정합니다.  
대상 테이블에서 삭제해야 합니다.
- *테이블로 이동할 익스텐션에 대한 쿼리* - 이 쿼리의 결과는 대상 테이블로 이동해야 하는 소스 테이블의 범위 ID를 지정합니다.

두 쿼리 모두 "ExtentId"라는 열이 있는 레코드 집합을 반환해야 합니다.

**제한 사항**
- 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다. 
- *테이블에서 삭제할 익스텐션에 대해 쿼리에서* 지정한 모든 익스텐션은 대상 테이블에 속할 것으로 예상됩니다.
- 원본 테이블의 모든 열은 이름과 데이터 형식이 같은 대상 테이블에 존재해야 합니다.

**출력 반환(동기화** 실행용)

출력 매개 변수 |Type |Description 
---|---|---
오리지널익시드 |문자열 |원본 테이블의 원래 익스텐트에 대한 고유 식별자(GUID) 또는 대상 테이블의 익스텐트(삭제된 범위)입니다.
결과익ID |문자열 |원본 테이블에서 대상 테이블로 이동된 결과 익스텐트에 대한 고유 식별자(GUID) 또는 대상 테이블에서 익스텐디가 삭제된 경우 비어 있습니다. 실패 시 - "실패".
세부 정보 |문자열 |작업이 실패할 경우 실패 세부 정보를 포함합니다.

**예**

2개의 특정 테이블 (,`MyTable1` `MyTable2`) 에서 `MyOtherTable`테이블로 모든 익스텐스를 `drop-by:MyTag`이동하고 `MyOtherTable` 태그가 지정된 모든 익스텐스를 삭제합니다.

```kusto
.replace extents in table MyOtherTable <|
    {.show table MyOtherTable extents where tags has 'drop-by:MyTag'},
    {.show tables (MyTable1,MyTable2) extents}
```

**예제 출력** 

|오리지널익시드 |결과익ID |세부 정보
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

*참고*: 
> [!NOTE]
> *테이블 쿼리에서 삭제할 익스텐션으로* 반환되는 익스텐트가 대상 테이블에 없으면 명령이 실패합니다. 대체 명령이 실행되기 전에 익스텐스가 병합된 경우 이러한 일이 발생할 수 있습니다. 누락된 익스텐트에서 명령이 실패하는지 확인하려면 쿼리가 예상된 ExtentIds를 반환하는지 확인합니다. 아래 #1 표 MyOtherTable에 드롭 할 범위가 없으면 실패합니다. 그러나 드롭 할 쿼리가 익스텐트 ID를 반환하지 않았기 때문에 드롭 할 범위가 존재하지 않더라도 예제 #2 성공합니다. 

예 #1: 

```kusto
.replace extents in table MyOtherTable <|
     { datatable(ExtentId:guid)[ "2cca5844-8f0d-454e-bdad-299e978be5df"] }, { .show table MyTable1 extents }
```

#2 예:

```kusto
.replace extents in table MyOtherTable  <|
     { .show table MyOtherTable extents | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) }, { .show table MyTable1 extents }
```



## <a name="drop-extent-tags"></a>.drop 익스텐트 태그

**구문**

`.drop`[`async` `extent` `tags` `from` ] `table` *표 이름* `(`'*Tag1*' [ '`,`*태그2*'`,`... `,`'*태그 ']*`)`

`.drop``tags``async` `extent` [ [ *쿼리*  <| 

* `async`(선택 사항)은 명령이 비동기적으로 실행되는지 여부를 지정합니다(이 경우 작업 ID(Guid)가 반환되고 [.show 작업](operations.md#show-operations) 명령을 사용하여 작업의 상태를 모니터링할 수 있습니다).
    * 이 옵션을 사용하는 경우 성공적인 실행 결과를 [.show 작업 세부 정보](operations.md#show-operation-details) 명령)을 검색할 수 있습니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행되며 제공된 [범위 태그와](extents-overview.md#extent-tagging) 태그를 포함하는 제공된 데이터베이스 및 테이블의 모든 익스텐트에서 삭제합니다.  

제거할 태그를 지정하는 방법에는 두 가지가 있습니다.

1. 지정된 테이블의 모든 익스텐트에서 제거해야 하는 태그를 명시적으로 지정합니다.
2. 결과가 테이블의 범위 아이디와 foreach 익스텐트(제거해야 하는 태그)를 지정하는 쿼리를 제공하여

**제한 사항**
- 모든 익스텐스는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다. 

**쿼리를 통해 익스텐트 지정**

관련된 모든 원본 및 대상 테이블에 대한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

```kusto 
.drop extent tags <| ...query... 
```

삭제할 익스텐트와 태그는 "ExtentId"라는 열과 "Tags"라는 열로 레코드 집합을 반환하는 Kusto 쿼리를 사용하여 지정됩니다. 

*참고*: [Kusto .NET 클라이언트 라이브러리를](../api/netfx/about-kusto-data.md)사용하는 경우 다음 방법을 사용하여 원하는 명령을 생성할 수 있습니다.
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**출력 반환**

출력 매개 변수 |Type |Description 
---|---|---
오리지널익시드 |문자열 |태그가 수정되고 작업의 일부로 삭제된 원래 익스텐트에 대한 고유 식별자(GUID) 
결과익ID |문자열 |태그를 수정한 결과 익스텐트에 대한 고유 식별자(GUID)입니다(작업의 일부로 생성및 추가됨). 실패 시 - "실패".
결과익태그 |문자열 |결과가 태그된 태그(남아 있는 경우)또는 작업이 실패할 경우 "null"인 태그 컬렉션입니다.
세부 정보 |문자열 |작업이 실패할 경우 실패 세부 정보를 포함합니다.

**예**

태그가 `drop-by:Partition000` 지정된 테이블의 `MyOtherTable` 어느 범위에서든 태그를 삭제합니다.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

태그가 `drop-by:20160810104500` `a random tag` `drop-by:20160810` 태그된 테이블의 `My Table` 모든 범위에서 태그를 삭제합니다.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

표의 `drop-by` `MyTable`익스텐트에서 모든 태그를 삭제합니다.

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

표의 `MyTable`익스텐트에서 `drop-by:StreamCreationTime_20160915(\d{6})` 정규식과 일치하는 모든 태그를 삭제합니다.

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**예제 출력** 

|오리지널익시드 |결과익ID | 결과익태그 | 세부 정보
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | 파티션001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | 파티션001 파티션002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>.alter 익스텐트 태그

**구문**

`.alter`[`async` `extent` `tags` `(`[ '*Tag1*' [ '`,`*태그2*'`,`... `,`'*태그N*`)` <| ']*쿼리*

명령은 특정 데이터베이스의 컨텍스트에서 실행되며 지정된 쿼리에서 반환되는 모든 [익스텐션의 익스텐트 태그를](extents-overview.md#extent-tagging) 제공된 태그 집합으로 변경합니다.

변경할 익스텐트와 태그는 "ExtentId"라는 열로 레코드 집합을 반환하는 Kusto 쿼리를 사용하여 지정됩니다.

* `async`(선택 사항)은 명령이 비동기적으로 실행되는지 여부를 지정합니다(이 경우 작업 ID(Guid)가 반환되고 [.show 작업](operations.md#show-operations) 명령을 사용하여 작업의 상태를 모니터링할 수 있습니다).
    * 이 옵션을 사용하는 경우 성공적인 실행 결과를 [.show 작업 세부 정보](operations.md#show-operation-details) 명령)을 검색할 수 있습니다.

관련된 모든 테이블에 대한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.

**제한 사항**
- 모든 익스텐스는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다. 

**출력 반환**

출력 매개 변수 |Type |Description 
---|---|---
오리지널익시드 |문자열 |태그가 수정되고 작업의 일부로 삭제된 원래 익스텐트에 대한 고유 식별자(GUID) 
결과익ID |문자열 |태그를 수정한 결과 익스텐트에 대한 고유 식별자(GUID)입니다(작업의 일부로 생성및 추가됨). 실패 시 - "실패".
결과익태그 |문자열 |작업실패시 결과 익스텐션에 태그가 지정된 태그 또는 "null"의 집합입니다.
세부 정보 |문자열 |작업이 실패할 경우 실패 세부 정보를 포함합니다.

**예**

테이블의 `MyTable` 모든 익스텐츠의 태그를 `MyTag`로 변경합니다.

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

에 `MyTable`태그가 지정된 `drop-by:MyTag` `drop-by:MyNewTag` 테이블의 모든 익스텐스의 태그를 `MyOtherNewTag`변경합니다.

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**예제 출력** 

|오리지널익시드 |결과익ID | 결과익태그 | 세부 정보
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | 드롭 바이:마이뉴태그 마이에이어뉴태그| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | 드롭 바이:마이뉴태그 마이에이어뉴태그| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | 드롭 바이:마이뉴태그 마이에이어뉴태그| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | 드롭 바이:마이뉴태그 마이에이어뉴태그| 

