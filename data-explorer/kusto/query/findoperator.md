---
title: 연산자 찾기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 찾기 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 495eb15c13a5691df8b0a2f3c2996c5aac58eb0a
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663809"
---
# <a name="find-operator"></a>find 연산자

테이블 집합에서 조건자와 일치하는 행을 찾습니다.

::: zone pivot="azuredataexplorer"

의 범위는 `find` 크로스 데이터베이스 또는 크로스 클러스터 일 수도 있습니다.

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

* `find`[`withsource`=*[열 이름]* [`in` `(` *Table* 표`,` [ *테이블*, ...] `)`] `where` `:``project-smart`  |  `project` *[열이름* `:`[*열 유형]*[`,` *열 이름*[*열 유형*], ...] *Predicate* [`,` `pack(*)`]] 

* `find`*조건자* [`project-smart`  |  `project` 열`:`이름 [`,` *열**유형*] [ *열 이름*[`:`열*유형*], ...] [`, pack(*)`]] 

## <a name="arguments"></a>인수

::: zone pivot="azuredataexplorer"

* `withsource=`*열 이름*: 선택 사항입니다. 기본적으로 출력에는 *source_라는* 열이 포함되며, 그 값은 각 행에 기여한 소스 테이블을 나타냅니다. 이 지정하면 source_ 대신 *ColumnName이* *사용됩니다.*
쿼리가 두 개 이상의 데이터베이스의 테이블을 효과적으로 참조하는 경우(기본 데이터베이스는 항상 계산) 이 열의 값에는 데이터베이스로 한정된 테이블 이름이 지정됩니다. 마찬가지로 두 개 이상의 __클러스터를__ 참조하는 경우 클러스터 및 데이터베이스 자격도 값에 표시됩니다.
* *조건자*: [세부 사항을 참조하십시오.](./findoperator.md#predicate-syntax) `boolean` 입력 테이블 *테이블* `,` *[테이블 [...]의*열에 대한 [식입니다.](./scalar-data-types/bool.md) 각 입력 테이블의 각 행에 대해 평가됩니다. 
* `Table`: 선택 사항입니다. 기본적으로 *find는* 현재 데이터베이스의 모든 테이블에서 검색됩니다.
    *  테이블 의 이름(예: `Events` 또는
    *  `(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*` 이름이 시작되는 `E`데이터베이스의 모든 테이블의 공용 구조체를 형성합니다.
* `project-smart` | `project`: 지정되지 `project-smart` 않은 경우 [세부 사항을 참조하여](./findoperator.md#output-schema) 기본적으로 사용됩니다.

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*열 이름*: 선택 사항입니다. 기본적으로 출력에는 *source_라는* 열이 포함되며, 그 값은 각 행에 기여한 소스 테이블을 나타냅니다. 이 지정하면 source_ 대신 *ColumnName이* *사용됩니다.*
* *조건자*: [세부 사항을 참조하십시오.](./findoperator.md#predicate-syntax) `boolean` 입력 테이블 *테이블* `,` *[테이블 [...]의*열에 대한 [식입니다.](./scalar-data-types/bool.md) 각 입력 테이블의 각 행에 대해 평가됩니다. 
* `Table`: 선택 사항입니다. 기본적으로 *찾기는* 모든 테이블에서 검색됩니다.
    *  테이블 의 이름(예: `Events` 또는
    *  `(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*` 이름이 시작되는 `E`모든 테이블의 공용 구조체가 형성됩니다.
* `project-smart` | `project`: 지정되지 `project-smart` 않은 경우 [세부 사항을 참조하여](./findoperator.md#output-schema) 기본적으로 사용됩니다.

::: zone-end

## <a name="returns"></a>반환

*조건어가* 있는 *표* `,` *[표*, ...] `true`의 행 변환. 행은 아래에 설명된 출력 스키마에 따라 변환됩니다.

## <a name="output-schema"></a>출력 스키마

**source_ 열**

find 연산자 출력에는 항상 소스 테이블 이름이 있는 *source_* 열이 포함됩니다. 매개 변수를 사용하여 열의 `withsource` 이름을 바꿀 수 있습니다.

**결과 열**

조건자 평가에서 사용되는 열을 포함하지 않는 원본 테이블은 필터링됩니다.

을 `project-smart`사용할 때 출력에 표시되는 열은 다음과 같습니다.
1. 술어에 명시적으로 나타나는 열
2. 모든 필터링된 테이블에 공통되는 열 열 나머지 열은 속성 가방에 압축되고 추가 `pack_` 열에 나타납니다.
술어에 의해 명시적으로 참조 되고 여러 형식의 여러 테이블에 나타나는 열은 이러한 각 형식에 대한 결과 스키마에 다른 열을 갖습니다. 각 열 이름은 원래 열 이름과 밑줄로 구분된 형식에서 생성됩니다.

열 `project` *이름*[`:`*열 유형]*`,` [ 열 이름 [ *열 유형*]을`:`*ColumnType*사용하는 경우 ...] [`,` `pack(*)`]:
1. 결과 테이블에는 목록에 지정된 열이 포함됩니다. 원본 테이블에 특정 열이 없는 경우 해당 행의 값은 null이 됩니다.
2. *ColumnName과*함께 *ColumnType을* 지정할 때 **결과의** 이 열에는 지정된 형식이 있으며 필요한 경우 해당 형식에 값이 캐스팅됩니다. *조건어를*평가할 때 열 형식에는 영향을 주지 않습니다.
3. 사용 `pack(*)` 되면 나머지 열은 속성 가방에 포장되고 추가 `pack_` 열에 나타납니다.

**pack_ 열**

이 열에는 출력 스키마에 나타나지 않는 모든 열의 데이터가 포함된 속성 가방이 포함됩니다. 원본 열 이름은 속성 이름으로, 열 값은 속성 값으로 게재됩니다.

## <a name="predicate-syntax"></a>조건부 구문

일부 필터링 함수 요약은 [where 연산자참조하십시오.](./whereoperator.md)

find 연산자는 `* has` *용어에* 대한 대체 구문을 *지원하며, 용어만* 사용하면 모든 입력 열에서 용어를 검색합니다.

## <a name="notes"></a>메모

* 절이 `project` 여러 테이블에 나타나고 여러 형식이 있는 열을 참조하는 경우 형식은 project 절에서 이 열 참조를 따라야 합니다.
* 열이 여러 테이블에 나타나고 여러 `project-smart` 형식이 있고 사용 중인 경우 [union에](./unionoperator.md) `find`설명된 대로 결과의 각 형식에 해당하는 열이 있습니다.
* *project-smart를*사용하는 경우 조건자, 소스 테이블 집합 또는 테이블 스키마의 변경 내용이 출력 스키마를 변경할 수 있습니다. 상수 결과 스키마가 필요한 경우 *프로젝트를* 대신 사용하십시오.
* `find`범위는 [함수를 포함할](../management/functions.md)수 없습니다. 찾기 범위에 함수를 포함하려면 [보기 키워드를](./letstatement.md) 사용하여 [let 문을](./letstatement.md) 정의합니다.

## <a name="performance-tips"></a>성능 팁

* [테이블 형식 식과](./tabularexpressionstatements.md)반대로 [테이블을](../management/tables.md) 사용 - 테이블 형식 식의 경우 `union` find 연산자는 쿼리로 돌아가 성능이 저하될 수 있습니다.
* 여러 테이블에 나타나고 여러 형식이 있는 열이 프로젝트 절의 일부인 경우 테이블을 전달하기 전에 테이블을 수정하는 `find` 것보다 Project 절에 *ColumnType을* 추가하는 것이 좋습니다(이전 팁 참조).
* 조건자에 시간 기반 필터 추가(datetime 열 값 또는 [ingestion_time()](./ingestiontimefunction.md)사용)
* 전체 텍스트 검색을 통해 특정 열에서 검색하는 것을 선호합니다. 
* 여러 테이블에 나타나고 여러 형식이 있는 명시적으로 열을 참조하지 않는 것이 좋습니다. 조건자가 하나 이상의 형식에 대해 이러한 열 형식을 해결할 때 유효한 경우 쿼리는 공용 구조체로 대체됩니다.

[예제 보기](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>예

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>모든 테이블에서 용어 조회(현재 데이터베이스)

다음 쿼리는 모든 열에 단어가 포함된 현재 데이터베이스의 모든 `Kusto`테이블에서 모든 행을 찾습니다. 결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>이름 패턴과 일치하는 모든 테이블에서 용어 조회(현재 데이터베이스)

다음 쿼리는 이름이 `K`로 시작하고 모든 열에 단어가 `Kusto`포함된 현재 데이터베이스의 모든 테이블에서 모든 행을 찾습니다.
결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>클러스터의 모든 데이터베이스의 모든 테이블에서 용어 조회

다음 쿼리는 모든 열에 단어가 포함된 모든 데이터베이스의 모든 `Kusto`테이블에서 모든 행을 찾습니다. 이것은 [교차 데이터베이스 쿼리 쿼리입니다.](./cross-cluster-or-database-queries.md) 결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>이름 패턴과 일치하는 모든 테이블 및 데이터베이스에 대한 용어 조회(클러스터)

다음 쿼리는 이름이 `K` `B` 시작되고 모든 열에 단어가 `Kusto`포함된 모든 데이터베이스에서 이름이 시작되는 모든 테이블의 모든 행을 찾습니다. 결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>여러 클러스터의 용어 조회

다음 쿼리는 이름이 `K` `B` 시작되고 모든 열에 단어가 `Kusto`포함된 모든 데이터베이스에서 이름이 시작되는 모든 테이블의 모든 행을 찾습니다. 결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>모든 테이블에서 용어 조회

다음 쿼리는 모든 열에 단어가 `Kusto`포함된 모든 테이블에서 모든 행을 찾습니다. 결과 레코드는 [출력 스키마에](#output-schema)따라 변환됩니다. 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>`find` 출력 결과의 예  

다음 예제는 이벤트Table1 및 EventsTable2라는 두 테이블을 통해 사용하는 방법을 `find` 보여 주며 있습니다. 이 두 테이블의 다음 내용이 있다고 가정합니다. 

### <a name="eventstable1"></a>이벤트테이블1

|Session_Id|Level|이벤트 텍스트|버전
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 텍스트1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|일부 텍스트2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|일부 텍스트3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|정보|일부 텍스트4|v1.1.0

### <a name="eventstable2"></a>이벤트테이블2

|Session_Id|Level|이벤트 텍스트|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|정보|일부 기타 텍스트1|이벤트 1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 기타 텍스트2|이벤트 2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|일부 기타 텍스트3|이벤트 3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Error|일부 기타 텍스트4|이벤트 4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>공통 열에서 검색하고 공통 열과 드문 열을 투영하고 나머지는 압축합니다.  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|이벤트 텍스트|버전|EventName|pack_
|---|---|---|---|---|
|이벤트테이블1|일부 텍스트2|v1.0.0||{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "레벨":"오류"}
|이벤트테이블2|일부 기타 텍스트3||이벤트 3|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "레벨":"오류"}


### <a name="search-in-common-and-uncommon-columns"></a>공통 및 드문 열에서 검색

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|이벤트 텍스트|버전|EventName|
|---|---|---|---|---|
|이벤트테이블1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|일부 텍스트1|v1.0.0
|이벤트테이블1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|일부 텍스트2|v1.0.0
|이벤트테이블2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|일부 기타 텍스트1||이벤트 1

참고: 실제로 *EventsTable1* 행은 조건자로 ```Version == 'v1.0.0'``` 필터링되고 *EventsTable2* 행은 조건어로 ```EventName == 'Event1'``` 필터링됩니다.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>축약된 표기와 현재 데이터베이스의 모든 테이블에서 검색

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|이벤트 텍스트|pack_|
|---|---|---|---|---|
|이벤트테이블1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 텍스트1|{"버전":"v1.0.0"}
|이벤트테이블1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|일부 텍스트2|{"버전":"v1.0.0"}
|이벤트테이블2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|정보|일부 기타 텍스트2|{"이벤트 이름":"이벤트2"}
|이벤트테이블2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|일부 기타 텍스트3|{"이벤트 이름":"이벤트3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>각 행의 결과를 속성 백으로 반환합니다.

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|이벤트테이블1|{"Session_Id":"acbd207d-51aaa-4df7-bfa7-be70eb68f04e", "레벨":"정보", "이벤트 텍스트":"일부 텍스트1", "버전":"v1.0"}
|이벤트테이블1|{"Session_Id":"acbd207d-51aaa-4df7-bfa7-be70eb68f04e", "레벨":"오류", "EventText":"일부 텍스트2", "버전":"v1.0.0"}
|이벤트테이블2|{"Session_Id":"acbd207d-51aaa-4df7-bfa7-be70eb68f04e", "레벨":"정보", "이벤트텍스트":"일부 다른 텍스트2", "EventName":"Event2"}
|이벤트테이블2|{"Session_Id":"acbd207d-51aaa-4df7-bfa7-be70eb68f04e", "레벨":"오류", "EventText":"일부 다른 텍스트3", "EventName":"Event3"}


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>다음과 같이 `find` 수행될 사례의 예`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>비 표식 식을 찾기 로 사용

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>여러 테이블에 나타나고 여러 형식이 있는 열 참조

다음을 실행하여 두 개의 테이블을 만들었다고 가정합니다. 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 다음 쿼리는 다음과 `union`같이 실행됩니다.
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

그리고 출력 결과 스키마 __(레벨 : 문자열, 타임 스탬프, ProcessId_string, ProcessId_int)__

* 다음 쿼리는 실행되지만 `union` 다른 결과 스키마를 생성합니다.
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

그리고 출력 결과 스키마 __(레벨 : 문자열, 타임 스탬프, ProcessId_string)__ 될 것입니다