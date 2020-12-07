---
title: in 및 notin 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 in 및 notin 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513253"
---
# <a name="in-and-in-operators"></a>in 및 !in 연산자

제공된 값 세트를 기반으로 레코드 집합을 필터링합니다.

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * 연산자에 '~'를 추가하면 값 검색에서 대/소문자를 구분하지 않습니다(`x in~ (expression)` 또는 `x !in~ (expression)`).
> * 테이블 형식 식에서 결과 집합의 첫 번째 열이 선택됩니다.
> * 식 목록에서 최대 `1,000,000` 값을 생성할 수 있습니다.
> * 중첩된 배열은 값의 단일 목록으로 병합됩니다. 예를 들어 `x in (dynamic([1,[2,3]]))`는 `x in (1,2,3)`가 됩니다.
 
## <a name="syntax"></a>Syntax

### <a name="case-sensitive-syntax"></a>대/소문자 구분 구문

*T* `|` `where` *col* `in` `(`*스칼라 식의 목록*`)`   
*T* `|` `where` *col* `in` `(`*테이블 형식 식*`)`   
 
*T* `|` `where` *col* `!in` `(`*스칼라 식의 목록*`)`  
*T* `|` `where` *col* `!in` `(`*테이블 형식 식*`)`   

### <a name="case-insensitive-syntax"></a>대/소문자를 구분하지 않는 구문

*T* `|` `where` *col* `in~` `(`*스칼라 식의 목록*`)`   
*T* `|` `where` *col* `in~` `(`*테이블 형식 식*`)`   
 
*T* `|` `where` *col* `!in~` `(`*스칼라 식의 목록*`)`  
*T* `|` `where` *col* `!in~` `(`*테이블 형식 식*`)`   

## <a name="arguments"></a>인수

* *T* - 레코드를 필터링할 테이블 형식 입력입니다.
* *col* - 필터링할 열입니다.
* *식 목록* - 테이블 형식, 스칼라 또는 리터럴 식의 쉼표로 구분된 목록입니다.
* *테이블 형식 식* - 값 세트가 있는 테이블 형식 식입니다. 식에 여러 열이 있는 경우 첫 번째 열이 사용됩니다.

## <a name="returns"></a>반환

조건자가 `true`인 *T* 의 행입니다.

## <a name="examples"></a>예제  

### <a name="use-in-operator"></a>'in' 연산자 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|4775|  

### <a name="use-in-operator"></a>'in~' 연산자 사용  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|개수|
|---|
|4775|  

### <a name="use-in-operator"></a>'!in' 연산자 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|54291|  


### <a name="use-dynamic-array"></a>동적 배열 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|개수|
|---|
|3218|

### <a name="subquery"></a>하위 쿼리

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

동일한 쿼리를 다음과 같이 작성할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|개수|
|---|
|14242|  

### <a name="top-with-other-example"></a>Top(기타 예제 포함)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| 주     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| WISCONSIN | 31                   |
| 텍사스     | 55                   |
| FLORIDA   | 85                   |
| GEORGIA   | 106                  |
| 기타     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>함수에서 반환된 정적 목록 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|개수|
|---|
|4775|  

함수 정의.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Name|매개 변수|본문|폴더|DocString|
|---|---|---|---|---|
|InterestingStates|()|{ dynamic(["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"]) }
