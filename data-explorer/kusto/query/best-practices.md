---
title: 쿼리 모범 사례-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 모범 사례를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128821"
---
# <a name="best-practices"></a>모범 사례 

## <a name="general"></a>일반

쿼리가 더 빠르게 실행 되도록 하기 위해 수행할 수 있는 몇 가지 "Dos 및 일과"가 있습니다.

### <a name="do"></a>해야 할 일

*   먼저 시간 필터를 사용합니다. Kusto는 시간 필터를 활용 하도록 최적화 되어 있습니다.
*   문자열 연산자를 사용 하는 경우:
    *   `has` `contains` 전체 토큰을 찾을 때는 연산자를 사용 하는 것이 좋습니다. `has`는 부분 문자열을 조회할 필요가 없기 때문에 성능이 더 우수 합니다.
    *   해당 하는 경우 대/소문자를 구분 하는 연산자를 사용 하는 것이 더 좋습니다. 예를 들어 over, over 및 over를 사용 하는 것이 좋습니다 `==` `=~` `in` `in~` `contains_cs` `contains` . 하지만 모두 사용 하지 않고를 사용 하는 것이 `contains` / `contains_cs` `has` / `has_cs` 훨씬 더 낫습니다.
*   `*`모든 열에서 전체 텍스트 검색을 사용 하는 대신 특정 열을 찾는 것이 좋습니다.
*   대부분의 쿼리가 수백만 개의 행에서 [동적 개체](./scalar-data-types/dynamic.md) 의 필드를 추출 하는 것을 발견 하는 경우 수집 시이 열을 구체화 하는 것이 좋습니다. 이러한 방식으로 열 추출에 대해 한 번만 지불 하면 됩니다.  
*   `let`두 번 이상 사용 하는 값에 문이 있는 경우 [구체화 () 함수](./materializefunction.md) 를 사용 하는 것이 좋습니다 (사용에 대 한 몇 가지 [모범 사례](#materialize-function) 참조 `materialize()` ).

### <a name="dont"></a>안 함

*   또는 끝에 새 쿼리를 사용해 서 `limit [small number]` `count` 는 안 됩니다.
    알 수 없는 데이터 집합에 대해 바인딩되지 않은 쿼리를 실행 하면 클라이언트에 반환 되는 결과의 Gb이 생성 되어 응답 속도가 느려지고 클러스터가 사용 중이 됩니다.
*   10억 레코드를 초과 하는 변환 (JSON, 문자열 등)을 적용 하는 경우 변환에 공급 되는 데이터의 양을 줄이기 위해 쿼리를 변형 합니다.
*   `tolower(Col) == "lowercasestring"`대/소문자를 구분 하지 않는 비교를 수행 하려면를 사용 하지 마세요. 대신 `Col =~ "lowercasestring"` 을(를) 사용하세요.
    *   데이터가 이미 소문자 (또는 대문자) 이면 대/소문자를 구분 하지 않는 비교를 사용 하지 말고 `Col == "lowercasestring"` 대신 (또는 `Col == "UPPERCASESTRING"` )를 사용 합니다.
*   테이블 열을 기준으로 필터링 할 수 있는 경우 계산 열에 대해 필터링 하지 않습니다. 즉 `T | extend _value = <expression> | where predicate(_value)` , 대신을 수행 `T | where predicate(<expression>)` 합니다.

## <a name="summarize-operator"></a>summarize 연산자

*   요약 연산자의 group by 키가 높은 카디널리티 (모범 사례: 100만 이상)를 사용 하는 경우 힌트를 사용 하는 것이 좋습니다 [. 전략 = 순서 섞기](./shufflequery.md).

## <a name="join-operator"></a>join 연산자

*   [Join 연산자](./joinoperator.md)를 사용 하는 경우 첫 번째 행이 있는 테이블 (맨 왼쪽)을 선택 합니다. 
*   클러스터 간에 [조인 연산자](./joinoperator.md) 데이터를 사용할 때 대부분의 데이터가 있는 조인의 "right" 쪽에서 쿼리를 실행 합니다.
*   왼쪽이 작은 경우 (최대 10만 개 레코드) 오른쪽이 큰 경우 [힌트. 전략 = 브로드캐스트](./broadcastjoin.md)를 사용 합니다.
*   조인의 양쪽 모두 너무 커서 조인 키가 높은 카디널리티를 사용 하는 경우 힌트를 사용 합니다 [. 전략 = 순서 섞기](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>parse 연산자 및 extract () 함수

*   [parse 연산자](./parseoperator.md) (simple mode)는 대상 열의 값에 모두 동일한 형식이 나 패턴을 공유 하는 문자열이 포함 된 경우에 유용 합니다.
예를 들어와 같은 값이 있는 열의 경우 `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` 각 필드의 값을 추출 하는 경우 `parse` 여러 문 대신 연산자를 사용 `extract()` 합니다.
*   [extract () 함수](./extractfunction.md) 는 구문 분석 된 문자열이 모두 동일한 형식이 나 패턴을 따르지 않는 경우에 유용 합니다.
이러한 경우 REGEX를 사용 하 여 필요한 값을 추출 합니다.

## <a name="materialize-function"></a>구체화 () 함수

*   [구체화 () 함수](./materializefunction.md)를 사용 하는 경우 구체화 된 데이터 집합을 줄이고 쿼리의 의미 체계를 유지 하는 모든 가능한 연산자를 푸시합니다. 예를 들어, 필터 또는 project only는 필수 열입니다.
    
    **예:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* 텍스트에 대 한 필터는 상호 하 고 구체화 식에 푸시할 수 있습니다.
    쿼리에는 이러한 열만 필요 `Timestamp` `Text` `Resource1` `Resource2` 하므로 구체화 된 식 안에 이러한 열을 프로젝션 하는 것이 좋습니다.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   필터가이 쿼리와 동일 하지 않으면 다음을 수행 합니다.  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   결합 된 필터가 구체화 된 결과를 크게 줄이는 경우 아래 쿼리와 같이 구체화 된 결과에 대 한 두 필터를 논리 식으로 결합 하는 것이 유용할 수 있습니다 `or` . 그러나 쿼리의 의미 체계를 유지 하기 위해 각 union 레그의 필터를 유지 합니다.
     
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
    