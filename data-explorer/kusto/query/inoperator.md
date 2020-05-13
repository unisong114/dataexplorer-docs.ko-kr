---
title: in 및 notin 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 및 notin 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: cd11362c15e5ecfb80eab57b57b22f190f47da05
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271573"
---
# <a name="in-and-in-operators"></a>in 및 !in 연산자

제공 된 값 집합을 기반으로 레코드 집합을 필터링 합니다.

```kusto
Table1 | where col in ('value1', 'value2')
```

**구문**

*대/소문자 구분 구문:*

*T* `|` `where` *col* `in` `(` *스칼라 식의* T 열 목록`)`   
*T* `|` `where` *col* `in` `(` *테이블 형식 식*`)`   
 
*T* `|` `where` *col* `!in` `(` *스칼라 식의* T 열 목록`)`  
*T* `|` `where` *col* `!in` `(` *테이블 형식 식*`)`   

*대/소문자를 구분 하지 않는 구문:*

*T* `|` `where` *col* `in~` `(` *스칼라 식의* T 열 목록`)`   
*T* `|` `where` *col* `in~` `(` *테이블 형식 식*`)`   
 
*T* `|` `where` *col* `!in~` `(` *스칼라 식의* T 열 목록`)`  
*T* `|` `where` *col* `!in~` `(` *테이블 형식 식*`)`   

**인수**

* *T* -레코드를 필터링 할 테이블 형식 입력입니다.
* *col* -필터링 할 열입니다.
* *식 목록* -쉼표로 구분 된 테이블 형식, 스칼라 또는 리터럴 식 목록입니다.  
* *테이블 형식 식* -값 집합을 포함 하는 테이블 형식 식입니다. 사례 식에는 여러 열이 있고 첫 번째 열은 사용 됩니다.

**반환**

조건자가 인 *T* 의 행`true`

**참고 사항**

* 식 목록에 최대 값이 생성 될 수 있습니다. `1,000,000`    
* 중첩 된 배열은 값의 단일 목록으로 평면화 됩니다. 예를 `x in (dynamic([1,[2,3]]))` 들어`x in (1,2,3)` 
* 테이블 형식 식의 경우 결과 집합의 첫 번째 열이 선택 됩니다.   
* 연산자에 ' ~ '를 추가 하면 값이 검색 대/소문자를 구분 하지 `x in~ (expression)` 않습니다. 또는 `x !in~ (expression)` 입니다.

**예:**  

**' In ' 연산자를 간단 하 게 사용 합니다.**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|4775|  


**' In ~ ' 연산자를 간단 하 게 사용 합니다.**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|개수|
|---|
|4775|  

**'! In ' 연산자를 간단 하 게 사용 합니다.**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|54291|  


**동적 배열 사용:**

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


**하위 쿼리 예제:**  

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

**Top (기타 예:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| State     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| 위스콘신 | 31                   |
| 텍사스     | 55                   |
| 플로리다   | 85                   |
| 그루지야   | 106                  |
| 기타     | 415                  |

**함수에서 반환 되는 정적 목록 사용:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|개수|
|---|
|4775|  


함수 정의는 다음과 같습니다.  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|속성|매개 변수|본문|폴더|DocString|
|---|---|---|---|---|
|InterestingStates|()|{dynamic (["워싱턴", "플로리다", "그루지야", "뉴욕"])}}
