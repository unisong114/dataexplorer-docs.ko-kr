---
title: in 및 notin 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 in 및 notin 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513807"
---
# <a name="in-and-in-operators"></a>in 및 !in 연산자

제공된 값 집합을 기반으로 레코드 집합을 필터링합니다.

```kusto
Table1 | where col in ('value1', 'value2')
```

**구문**

*대/소문자 구분 구문:*

*Scalar 식의* *T* `|` `where` *콜* `in` `(`목록`)`   
*T* `|` T `where` *col* `in` 콜 `(` *표식 식*`)`   
 
*Scalar 식의* *T* `|` `where` *콜* `!in` `(`목록`)`  
*T* `|` T `where` *col* `!in` 콜 `(` *표식 식*`)`   

*대/소문자 구분 되지 않은 구문:*

*Scalar 식의* *T* `|` `where` *콜* `in~` `(`목록`)`   
*T* `|` T `where` *col* `in~` 콜 `(` *표식 식*`)`   
 
*Scalar 식의* *T* `|` `where` *콜* `!in~` `(`목록`)`  
*T* `|` T `where` *col* `!in~` 콜 `(` *표식 식*`)`   

**인수**

* *T* - 레코드를 필터링할 테이블 형식 입력입니다.
* *col* - 필터링할 열입니다.
* *식 목록* - 표, 스칼라 또는 리터럴 식의 쉼표 분리 목록  
* *테이블 형식 식* - 값 집합이 있는 테이블 형식 식(대/소문자 식에 여러 열이 있으며 첫 번째 열이 사용됩니다).

**반환**

술어가 있는 *T행*`true`

**참고 사항**

* 식 목록은 최대 값을 `1,000,000` 생성할 수 있습니다.    
* 중첩된 배열은 단일 값 목록으로 병합됩니다(예: `x in (dynamic([1,[2,3]]))``x in (1,2,3)` 
* 테이블 형식 식의 경우 결과 집합의 첫 번째 열이 선택됩니다.   
* 연산자에 '~'를 추가하면 값 `x in~ (expression)` `x !in~ (expression)`검색 사례가 민감하지 않습니다.

**예:**  

**'in' 연산자의 간단한 사용:**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|4775|  


**'in~' 연산자의 간단한 사용:**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|개수|
|---|
|4775|  

**'!에서'연산자의 간단한 사용 :**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|개수|
|---|
|54291|  


**동적 배열 사용:**
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

**다른 예제와 함께 맨 위에:**  

```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| 시스템 상태     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| 위스콘신 | 31                   |
| 텍사스     | 55                   |
| 플로리다   | 85                   |
| 그루지야   | 106                  |
| 기타     | 415                  |

**함수에서 반환되는 정적 목록 사용:**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|개수|
|---|
|4775|  


함수 정의는 다음과 같습니다.  

```kusto
.show function InterestingStates
```

|이름|매개 변수|본문|폴더|닥스트링 (것)과 함께|
|---|---|---|---|---|
|흥미로운 상태|()|{ 동적(["워싱턴", "플로리다", "조지아", "뉴욕"]) }
