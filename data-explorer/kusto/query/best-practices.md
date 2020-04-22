---
title: 쿼리 모범 사례 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 모범 사례에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663866"
---
# <a name="query-best-practices"></a>쿼리 모범 사례 

## <a name="general"></a>일반

쿼리를 더 빠르게 실행할 수 있도록 따라야 할 몇 가지 "해야 할 일과 하지 말미"가 있습니다.

### <a name="do"></a>해야 할 일

*   먼저 시간 필터를 사용합니다. Kusto는 시간 필터를 활용하도록 매우 최적화되어 있습니다.
*   문자열 연산자 사용 시:
    *   전체 `has` 토큰을 검색할 때 연산자보다 `contains` 선호합니다. `has`하위 문자열을 조회할 필요가 없기 때문에 성능이 더 높습니다.
    *   대/소문자를 구분하는 연산자는 성능이 더 우수하므로 해당되는 경우 사용 중지합니다. 예를 들어 , `==` `=~`위에 `in` `in~`를 `contains_cs` 사용하는 `contains` 것을 선호합니다 `contains` / `contains_cs` (그러나 전부 `has` / `has_cs`피하고 사용할 수 있다면 더 좋습니다).
*   (모든 열에 걸쳐 전체 `*` 텍스트 검색)을 사용하는 것보다 특정 열을 찾는 것을 선호합니다.
*   대부분의 쿼리가 수백만 행에 걸쳐 [동적 개체에서](./scalar-data-types/dynamic.md) 필드 추출을 처리하는 경우 이 열을 인레이션 시간에 구체화하는 것이 좋습니다. 이렇게 하면 열 추출에 대해 한 번만 지불합니다.  
*   두 번 이상 사용하는 값을 문으로 사용하는 경우 [materialize() 함수를](./materializefunction.md) 사용하는 [best practices](#materialize-function) 것이 좋습니다(사용에 `materialize()`대한 몇 가지 모범 사례 참조). `let`

### <a name="dont"></a>하지 않아야 할 일

*   새 쿼리를 시도하지 `limit [small number]` 않거나 `count` 끝에 시도하지 마십시오.
    알 수 없는 데이터 집합에서 언바운드 쿼리를 실행하면 결과의 GB가 클라이언트로 반환되어 응답 속도가 느려지고 클러스터가 사용 중일 수 있습니다.
*   10억 개 이상의 레코드를 사용하여 전환(JSON, 문자열 등)을 적용하는 경우 쿼리를 변경하여 변환에 공급되는 데이터의 양을 줄입니다.
*   대/소문자 `tolower(Col) == "lowercasestring"` 구분 되지 않는 비교를 수행 하는 데 사용 하지 마십시오. 대신 `Col =~ "lowercasestring"` 을(를) 사용하세요.
    *   데이터가 소문자(또는 대문자)에 이미 있는 경우 대/소문자 구분되지 `Col == "UPPERCASESTRING"`않는 비교를 사용하지 말고 대신 (또는) 사용하십시오. `Col == "lowercasestring"`
*   테이블 열을 필터링할 수 있는 경우 계산된 열을 필터링하지 마십시오. 즉, `T | extend _value = <expression> | where predicate(_value)`을 대신 `T | where predicate(<expression>)`수행합니다.

## <a name="summarize-operator"></a>summarize 연산자

*   요약 연산자의 키별 그룹이 카디널리티가 높은 경우(모범 사례: 1백만 이상) [hint.strategy=셔플을](./shufflequery.md)사용하는 것이 좋습니다.

## <a name="join-operator"></a>join 연산자

*   [조인 연산자](./joinoperator.md)사용 하는 경우 첫 번째 행(가장 왼쪽)으로 더 적은 행이 있는 테이블을 선택합니다. 
*   클러스터 간에 [조인 연산자](./joinoperator.md) 데이터를 사용하는 경우 대부분의 데이터가 있는 조인의 "오른쪽" 쪽에서 쿼리를 실행합니다.
*   왼쪽이 작고(최대 100,000개의 레코드) 오른쪽이 큰 경우 [hint.strategy=브로드캐스트를](./broadcastjoin.md)사용합니다.
*   조인의 양면이 너무 크고 조인 키가 카디널리티가 높은 경우 [hint.strategy=셔플을](./shufflequery.md)사용합니다.
    
## <a name="parse-operator-and-extract-function"></a>연산자 및 extract() 함수 구문 분석

*   [구문 분석 연산자(단순](./parseoperator.md) 모드)는 대상 열의 값에 모두 동일한 형식이나 패턴을 공유하는 문자열이 포함되어 있는 경우에 유용합니다.
예를 들어 각 필드의 값을 `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`추출할 때 와 같은 값이 `parse` 있는 열의 `extract()` 경우 여러 문 대신 연산자를 사용합니다.
*   [extract() 함수는](./extractfunction.md) 구문 분석된 문자열이 모두 동일한 형식이나 패턴을 따르지 않는 경우에 유용합니다.
이러한 경우 REGEX를 사용하여 필요한 값을 추출합니다.

## <a name="materialize-function"></a>구체화() 기능

*   [materialize() 함수를](./materializefunction.md)사용하는 경우 구체화된 데이터 집합을 줄이고 쿼리의 의미 체계를 유지하는 가능한 모든 연산자를 푸시하려고 합니다. 예를 들어 필요한 열만 필터링하거나 투영합니다.
    
    **예제:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* Text의 필터는 서로 가며 구체화 식으로 푸시할 수 있습니다.
    쿼리에는 이러한 열만 `Timestamp` `Text` `Resource1` `Resource2` 있으므로 구체화된 식 내부에 이러한 열을 투영하는 것이 좋습니다.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   필터가 이 쿼리와 같이 동일하지 않은 경우:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   아래 쿼리에서와 같이 구체화된 `or` 결과에 두 필터를 결합하여 두 필터를 결합하는 것이 좋습니다(결합된 필터가 구체화된 결과를 크게 줄이는 경우). 그러나 쿼리의 의미 체계를 유지 하려면 각 공용 구조부 다리에 필터를 유지 합니다.
     
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
    