---
title: 구체화 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구체화 () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: 8858b261cb366842b475a76a1b2c3246b8a3e7b5
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264701"
---
# <a name="materialize"></a>materialize()

다른 하위 쿼리가 부분 결과를 참조할 수 있는 방식으로 쿼리 실행 시 하위 쿼리 결과를 캐시할 수 있습니다.
 
**구문**

`materialize(`*expression*`)`

**인수**

* *식*: 쿼리를 실행 하는 동안 평가 및 캐시할 테이블 형식 식입니다.

> [!NOTE]
> 구체화의 캐시 크기 제한은 **5gb**입니다. 이 제한은 클러스터 노드당 이며 동시에 실행 되는 모든 쿼리에 대해 상호 합니다. 쿼리에서를 사용 하 `materialize()` 고 캐시에서 더 이상 데이터를 보유할 수 없으면 쿼리가 오류와 함께 중단 됩니다.

>[!TIP]
>
>* 구체화 된 데이터 집합을 줄이고 쿼리의 의미 체계를 유지 하는 모든 가능한 연산자를 푸시합니다. 예를 들어 필터를 사용 하거나 필요한 열만 프로젝션 합니다.
>* 피연산자에 한 번만 실행할 수 있는 상호 하위 쿼리가 있을 때 join 또는 union과 함께 구체화를 사용 합니다. 예를 들어 조인/공용 구조체 포크 다리입니다. [Join 연산자 사용 예제를](#examples-of-query-performance-improvement)참조 하세요.
>* 구체화는 캐시 된 결과에 이름을 지정 하는 경우 let 문에서만 사용할 수 있습니다. [Let 문 사용 예제를](#examples-of-using-materialize)참조 하세요.

## <a name="examples-of-query-performance-improvement"></a>쿼리 성능 향상의 예

다음 예에서는를 사용 하 여 `materialize()` 쿼리 성능을 향상 시키는 방법을 보여 줍니다.
식은 `_detailed_data` 함수를 사용 하 여 정의 `materialize()` 되므로 한 번만 계산 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|상태|EventType|EventPercentage|이벤트|
|---|---|---|---|
|하와이 중인지|Waterspout|100|2|
|LAKE ONTARIO|해병대 뇌우 바람|100|8|
|알래스카 GULF|Waterspout|100|4|
|대서양 북부|해병대 뇌우 바람|95.2127659574468|179|
|LAKE E|해병대 뇌우 바람|92.5925925925926|25|
|동부 태평양|Waterspout|90|9|
|LAKE MICHIGAN|해병대 뇌우 바람|85.1648351648352|155|
|LAKE HURON|해병대 뇌우 바람|79.3650793650794|50|
|멕시코의 GULF|해병대 뇌우 바람|71.7504332755633|414|
|하와이|높은 서피스|70.0218818380744|320|


다음 예에서는 난수 집합을 생성 하 고를 계산 합니다. 
* 집합에 있는 고유 값의 수 ( `Dcount` )
* 집합에서 상위 3 개 값 
* 집합에 있는 이러한 모든 값의 합계입니다. 
 
[일괄 처리](batches.md) 및 구체화를 사용 하 여이 작업을 수행할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

결과 집합 1:  

|구해집니다|
|---|
|2578351|

결과 집합 2: 

|value|
|---|
|9999998|
|9999998|
|9999997|

결과 집합 3: 

|합계|
|---|
|15002960543563|

## <a name="examples-of-using-materialize"></a>구체화 () 사용 예

> [!TIP]
> 대부분의 쿼리가 수백만 개의 행에서 동적 개체의 필드를 추출 하는 경우 수집 시 열을 구체화 합니다.

`let`문을 두 번 이상 사용 하는 값으로 사용 하려면 [구체화 () 함수](./materializefunction.md)를 사용 합니다. 구체화 된 데이터 집합을 줄이고 쿼리의 의미 체계를 유지 하는 모든 가능한 연산자를 푸시합니다. 예를 들어 필터를 사용 하거나 필요한 열만 프로젝션 합니다.

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

에 대 한 필터는 `Text` 상호 이며 구체화 식에 푸시할 수 있습니다.
쿼리에는,, 및 열만 필요 `Timestamp` `Text` `Resource1` `Resource2` 합니다. 구체화 된 식 내에서 이러한 열을 프로젝션 합니다.
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
필터가 동일 하지 않으면 다음 쿼리와 같이 합니다.  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

결합 된 필터가 구체화 된 결과를 크게 줄이는 경우 아래 쿼리와 같이 구체화 된 결과에 대 한 두 필터를 논리 식으로 결합 합니다 `or` . 그러나 쿼리의 의미 체계를 유지 하려면 각 합집합 레그의 필터를 유지 합니다.
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
