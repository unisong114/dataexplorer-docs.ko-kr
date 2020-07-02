---
title: 찾기 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 찾기 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4be61920fe22c7b77eb54f849e86ba06a8bf533b
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763817"
---
# <a name="find-operator"></a>find 연산자

테이블 집합에서 조건자와 일치 하는 행을 찾습니다.

::: zone pivot="azuredataexplorer"

의 범위는 `find` 데이터베이스 간 또는 크로스 클러스터 일 수도 있습니다.

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>구문

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *table* [ `,` *table*, ...] `)` ] `where` *조건자* [ `project-smart`  |  `project` *columnname* [ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*], ...] [`,` `pack(*)`]] 

* `find`*조건자* [ `project-smart`  |  `project` *columnname*[ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*], ...] [`, pack(*)`]] 

## <a name="arguments"></a>인수

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: 선택 사항입니다. 기본적으로 출력에는 각 행을 제공한 원본 테이블을 나타내는 값이 *source_* 라는 열이 포함 됩니다. 지정 하는 경우 *source_* 대신 *ColumnName* 이 사용 됩니다.
와일드 카드 일치 후 쿼리에서 둘 이상의 데이터베이스 (기본 데이터베이스 포함)의 테이블을 참조 하는 경우이 열의 값에는 데이터베이스와 함께 정규화 된 테이블 이름이 포함 됩니다. 두 개 이상의 클러스터를 참조 하는 경우에는 유사한 *클러스터* 및 *데이터베이스* 의 자격도 값에 표시 됩니다.
* *조건자*: `boolean` [expression](./scalar-data-types/bool.md) 입력 테이블 *테이블* [ `,` *table*, ...]의 열에 대 한 식입니다. 각 입력 테이블의 각 행에 대해 평가 됩니다. 자세한 내용은 [조건자-구문 정보](./findoperator.md#predicate-syntax)를 참조 하세요.
* `Table`: 선택 사항입니다. 기본적으로 *찾기* 는 현재 데이터베이스의 모든 테이블에서 다음을 찾습니다.
    *  테이블의 이름 (예:)`Events`
    *  `(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어는 `E*` 데이터베이스에서 이름이로 시작 하는 모든 테이블의 합집합을 형성 합니다 `E` .
* `project-smart` | `project`: 지정 하지 않으면 `project-smart` 기본적으로가 사용 됩니다. 자세한 내용은 [출력 스키마 세부 정보](./findoperator.md#output-schema)를 참조 하세요.

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: 선택 사항입니다. 기본적으로 출력에는 각 행을 제공 하는 원본 테이블을 나타내는 값이 *source_* 라는 열이 포함 됩니다. 지정 하는 경우 *source_* 대신 *ColumnName* 이 사용 됩니다.
* *조건자*: `boolean` [expression](./scalar-data-types/bool.md) 입력 테이블 *테이블* [ `,` *table*, ...]의 열에 대 한 식입니다. 각 입력 테이블의 각 행에 대해 평가 됩니다. 자세한 내용은 [조건자-구문 정보](./findoperator.md#predicate-syntax)를 참조 하세요.
* `Table`: 선택 사항입니다. 기본적으로 *find* 는 다음에 대 한 모든 테이블을 검색 합니다.
    *  테이블의 이름 (예:)`Events` 
    *  `(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어은 `E*` 이름이로 시작 하는 모든 테이블의 합집합을 형성 합니다 `E` .
* `project-smart` | `project`: 지정 하지 않으면 `project-smart` 기본적으로 사용 됩니다. 자세한 내용은 [출력 스키마 세부 정보](./findoperator.md#output-schema)를 참조 하세요.

::: zone-end

## <a name="returns"></a>반환

*Table* `,` *조건자* 가 인 테이블 [ *table*, ...]의 행 변환 `true` 입니다. 행은 [출력 스키마](#output-schema)에 따라 변환 됩니다.

## <a name="output-schema"></a>출력 스키마입니다.

**source_ 열**

찾기 연산자 출력에는 항상 원본 테이블 이름이 포함 된 *source_* 열이 포함 됩니다. 매개 변수를 사용 하 여 열의 이름을 바꿀 수 있습니다 `withsource` .

**결과 열**

조건자 평가에 사용 되는 열이 포함 되지 않은 원본 테이블은 필터링 됩니다.

를 사용 하는 경우 `project-smart` 출력에 표시 되는 열은 다음과 같습니다.
* 조건자에 명시적으로 표시 되는 열입니다.
* 모든 필터링 된 테이블에 공통적으로 적용 되는 열입니다.

나머지 열은 속성 모음으로 압축 되며 추가 열에 표시 됩니다 `pack_` .
조건자에 의해 명시적으로 참조 되 고 여러 형식의 여러 테이블에 표시 되는 열은 이러한 각 형식에 대해 결과 스키마에 다른 열을 포함 합니다. 각 열 이름은 밑줄로 구분 된 원래 열 이름 및 형식에서 생성 됩니다.

`project` *Columnname*[ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*], ...]을 사용 하는 경우 [`,` `pack(*)`]:
* 결과 테이블에는 목록에 지정 된 열이 포함 됩니다. 원본 테이블에 특정 열이 포함 되어 있지 않으면 해당 행의 값이 null이 됩니다.
* *ColumnName*을 사용 하 여 *ColumnType* 를 지정 하는 경우 "result"의이 열에는 지정 된 형식이 지정 되 고, 필요한 경우 값이 해당 형식으로 캐스팅 됩니다. *조건자*를 평가할 때는 캐스팅이 열 형식에 영향을 주지 않습니다.
* `pack(*)`을 사용 하는 경우 나머지 열은 속성 모음에 압축 되며 추가 열에 표시 됩니다 `pack_` .

**pack_ 열**

이 열에는 출력 스키마에 표시 되지 않는 모든 열의 데이터가 포함 된 속성 모음이 포함 됩니다. 원본 열 이름은 속성 이름으로 사용 되 고 열 값은 속성 값으로 사용 됩니다.

## <a name="predicate-syntax"></a>조건자 구문

*Find* 연산자는 용어에 대 한 대체 구문을 지원 `* has` 하며 *용어*를 사용 하 여 모든 입력 열에서 용어를 검색 합니다.

일부 필터링 함수에 대 한 요약은 [where 연산자](./whereoperator.md)를 참조 하세요.

## <a name="notes"></a>참고

* `project`절이 여러 테이블에 표시 되는 열을 참조 하 고 형식이 여러 개인 경우에는 형식이 project 절에서이 열 참조 다음에와 야 합니다.
* 열이 여러 테이블에 표시 되 고 여러 형식이 있으며 `project-smart` 사용 중인 경우 `find` [union](./unionoperator.md) 에 설명 된 대로 결과의 각 형식에 해당 하는 열이 있습니다.
* *Project-smart*를 사용 하는 경우 조건자의 변경 내용, 원본 테이블 집합 또는 테이블 스키마에서 출력 스키마가 변경 될 수 있습니다. 상수 결과 스키마가 필요한 경우 대신 *project* 를 사용 하십시오.
* `find`범위는 [함수](../management/functions.md)를 포함할 수 없습니다. 찾기 범위에 함수를 포함 하려면 [view 키워드](./letstatement.md)를 사용 하 여 [let 문을](./letstatement.md) 정의 합니다.

## <a name="performance-tips"></a>성능 팁

* 테이블 [형식 식이](./tabularexpressionstatements.md)아닌 [테이블](../management/tables.md) 을 사용 합니다.
테이블 형식 식인 경우 find 연산자는 `union` 성능 저하를 일으킬 수 있는 쿼리로 대체 됩니다.
* 여러 테이블에 표시 되 고 형식이 여러 개인 열이 project 절의 일부 이면에 전달 하기 전에 테이블을 수정 하는 방법으로 *ColumnType* 를 프로젝트 절에 추가 하는 것이 좋습니다 `find` .
* 시간 기반 필터를 조건자에 추가 합니다. Datetime 열 값 또는 [ingestion_time ()](./ingestiontimefunction.md)를 사용 합니다.
* 전체 텍스트 검색 대신 특정 열에서 검색 합니다.
* 여러 테이블에 표시 되 고 여러 형식이 있는 열은 참조 하지 않는 것이 좋습니다. 두 개 이상의 형식에 대해 이러한 열 유형을 확인할 때 조건자가 유효 하면 쿼리는 다시 union으로 대체 됩니다.
예를 들어 [find가 union으로 작동 하는 경우의 예](./findoperator.md#examples-of-cases-where-find-will-act-as-union)를 참조 하세요.
 
## <a name="examples"></a>예제

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>현재 데이터베이스의 모든 테이블에서 용어 조회

이 쿼리는 현재 데이터베이스에 있는 모든 테이블에서 모든 열에 단어를 포함 하는 모든 행을 찾습니다 `Kusto` .
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>현재 데이터베이스에서 이름 패턴과 일치 하는 모든 테이블에서 용어 조회

이 쿼리는 이름이로 시작 하 고 열에 단어가 포함 된 현재 데이터베이스의 모든 테이블에서 모든 행을 찾습니다 `K` `Kusto` .
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>클러스터에 있는 모든 데이터베이스의 모든 테이블에서 용어 조회

이 쿼리는 모든 열에 단어를 포함 하는 모든 데이터베이스의 모든 테이블에서 모든 행을 찾습니다 `Kusto` .
이 쿼리는 [데이터베이스 간](./cross-cluster-or-database-queries.md) 쿼리입니다.
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>클러스터에서 이름 패턴과 일치 하는 모든 테이블 및 데이터베이스에서 용어 조회

이 쿼리는 `K` 이름이로 시작 `B` 하 고 열에 단어가 포함 된 모든 데이터베이스에서 이름이로 시작 하는 모든 테이블의 모든 행을 찾습니다 `Kusto` .
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>여러 클러스터에서 용어 조회

이 쿼리는 `K` 이름이로 시작 `B` 하 고 열에 단어가 포함 된 모든 데이터베이스에서 이름이로 시작 하는 모든 테이블의 모든 행을 찾습니다 `Kusto` .
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>모든 테이블에서 용어 조회

이 쿼리는 모든 열에 단어를 포함 하는 모든 테이블의 모든 행을 찾습니다 `Kusto` .
결과 레코드는 [출력 스키마](#output-schema)에 따라 변환 됩니다.

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>`find`출력 결과의 예  

다음 예에서는 `find` *EventsTable1* 및 *EventsTable2*두 테이블에서를 사용 하는 방법을 보여 줍니다.
이러한 두 테이블의 다음 내용이 있다고 가정 합니다.

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Level|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 Text1|v 1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|오류|일부 텍스트|v 1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|오류|일부 텍스트 3|v 1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|정보|일부 텍스트 4|v 1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Level|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|정보|다른 텍스트 1|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|다른 일부 텍스트|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|오류|다른 텍스트 3|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|오류|다른 텍스트 4|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>공통 열, 프로젝트 공통 및 일반 열에서 검색 하 고 나머지를 압축 합니다.  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|EventsTable1|일부 텍스트|v 1.0.0||{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}
|EventsTable2|다른 텍스트 3||Event3|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}


### <a name="search-in-common-and-uncommon-columns"></a>일반적이 고 일반적이 지 않은 열에서 검색

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|Version|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|일부 Text1|v 1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|일부 텍스트|v 1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|다른 텍스트 1||Event1

참고: 실제로 *EventsTable1* 행은 조건자를 사용 하 여 필터링 되 ```Version == 'v1.0.0'``` 고 *EventsTable2* 행은 조건자를 사용 하 여 필터링 됩니다 ```EventName == 'Event1'``` .

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>약식 표기를 사용 하 여 현재 데이터베이스의 모든 테이블에서 검색

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 Text1|{"Version": "v 1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|오류|일부 텍스트|{"Version": "v 1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|다른 일부 텍스트|{"EventName": "Event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|오류|다른 텍스트 3|{"EventName": "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>각 행의 결과를 속성 모음으로 반환

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Information", "EventText": "Some Text1", "Version": "v 1.0.0"}
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error", "EventText": "Some 텍스트", "Version": "v 1.0.0"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Information", "EventText": "Some Other Event2", "EventName": ""}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error", "EventText": "Some 다른 텍스트 3", "EventName": "Event3"}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>가의 역할을 하는 사례 예 `find``union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>테이블 형식이 아닌 식을 find 피연산자로 사용

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>여러 테이블에 표시 되 고 여러 형식이 있는 열 참조

다음을 실행 하 여 두 개의 테이블을 만들었다고 가정 합니다. 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 다음 쿼리는로 실행 됩니다 `union` .

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

출력 결과 스키마는 *(Level: string, Timestamp, ProcessId_string, ProcessId_int)* 입니다.

* 다음 쿼리도로 실행 `union` 되지만 다른 결과 스키마가 생성 됩니다.

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

출력 결과 스키마는 *(수준: 문자열, 타임 스탬프, ProcessId_string)입니다.*
