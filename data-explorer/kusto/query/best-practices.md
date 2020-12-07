---
title: 쿼리 모범 사례 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 쿼리 모범 사례에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.localizationpriority: high
ms.openlocfilehash: 762c3075c162ba35bdba539d0e86460c78f3297e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95511791"
---
# <a name="query-best-practices"></a>쿼리 모범 사례

다음은 쿼리를 더 빠르게 실행하기 위해 따라야 할 몇 가지 모범 사례입니다.

|작업  |기능  |사용하지 마세요.  |참고  |
|---------|---------|---------|---------|
| **시간 필터** | 먼저 시간 필터를 사용합니다. ||Kusto는 시간 필터를 사용하도록 고도로 최적화되어 있습니다.| 
|**문자열 연산자**      | `has` 연산자 사용     | `contains`는 사용하지 마세요.     | 전체 토큰을 찾을 때 `has`는 부분 문자열을 찾지 않으므로 더 잘 작동합니다.   |
|**대/소문자 구분 연산자**     |  `==` 사용       | `=~`은(는) 사용하지 마세요.       |  가능하면 대/소문자 구분 연산자를 사용합니다.       |
| | `in` 사용 | `in~`은(는) 사용하지 마세요.|
|  | `contains_cs` 사용         | `contains`는 사용하지 마세요.        | `has`/`has_cs`를 사용할 수 있고 `contains`/`contains_cs`를 사용하지 않을 수 있다면 훨씬 더 좋습니다. |
| **텍스트 검색**    |    특정 열에서 보기     |    `*`는 사용하지 마세요.    |   `*`는 모든 열에서 전체 텍스트 검색을 수행합니다.    |
| **수백만 개의 행의 [동적 개체](./scalar-data-types/dynamic.md)에서 필드 추출**    |  대부분의 쿼리가 수백만 개의 행의 동적 개체에서 필드를 추출하는 경우 수집 시간에 열을 구체화합니다.      |         | 이렇게 하면 열 추출에 대해 한 번만 지불하면 됩니다.    |
| **두 번 이상 사용하는 값이 있는 `let` 문** | [materialize() 함수](./materializefunction.md) 사용 |  |   `materialize()`를 사용하는 방법에 대한 자세한 내용은 [materialize()](materializefunction.md)를 참조하세요.|
| **10억 개를 초과하는 레코드에 변환 적용**| 쿼리를 변형하여 변환에 공급되는 데이터의 양을 줄입니다.| 피할 수 있는 경우에는 많은 양의 데이터를 변환하지 마세요. | |
| **새 쿼리** | 마지막에 `limit [small number]` 또는 `count`를 사용합니다. | |     알 수 없는 데이터 세트에 대해 바인딩되지 않은 쿼리를 실행하면 GB의 결과가 클라이언트에 반환되어 응답이 느려지고 클러스터를 많이 사용하게 됩니다.|
| **대/소문자를 구분하지 않는 비교** | `Col =~ "lowercasestring"` 사용 | `tolower(Col) == "lowercasestring"`은 사용하지 마세요. |
| **이미 소문자(또는 대문자)로 되어 있는 데이터 비교** | `Col == "lowercasestring"`(또는 `Col == "UPPERCASESTRING"`) | 대/소문자를 구분하지 않는 비교를 사용하지 마세요.||
| **열 필터링** |  테이블 열에 대해 필터링합니다.|계산 열을 필터링하지 마세요. | |
| | `T | where predicate(<expression>)` 사용 | `T | extend _value = <expression> | where predicate(_value)`는 사용하지 마세요. ||
| **summarize 연산자** |  summarize 연산자의 `group by keys`가 높은 카디널리티인 경우 [hint.strategy=shuffle](./shufflequery.md)을 사용합니다. | | 높은 카디널리티는 이상적으로 100만 이상입니다.|
|**[join 연산자](./joinoperator.md)** | 첫 번째 행이 될 행 수가 적은 테이블을 선택합니다(쿼리에서 가장 왼쪽). ||
| 클러스터 간 조인 |클러스터에서 대부분의 데이터가 있는 조인의 "오른쪽"에서 쿼리를 실행합니다. ||
|왼쪽이 작고 오른쪽이 클 때 조인 | [hint.strategy=broadcast](./broadcastjoin.md) 사용 || Small는 최대 100,000개의 레코드를 나타냅니다. |
|양쪽 모두 너무 클 때 조인 | [hint.strategy=shuffle](./shufflequery.md) 사용 || 조인 키의 카디널리티가 높을 때 사용합니다.|
|**동일한 형식 또는 패턴을 공유하는 문자열로 열에서 값 추출**|  [parse 연산자](./parseoperator.md) 사용 | 여러 가지 `extract()` 문을 사용하지 마세요.  | 예: `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`과 같은 값
|**[extract() 함수](./extractfunction.md)**| 구문 분석된 문자열이 모두 동일한 형식이나 패턴을 따르지 않을 때 사용합니다.| |REGEX를 사용하여 필요한 값을 추출합니다.|
| **[materialize() 함수](./materializefunction.md)** | 구체화된 데이터 세트를 줄이고 쿼리의 의미 체계를 유지하는 가능한 모든 연산자를 푸시합니다. | |예를 들어 필터 또는 프로젝트만 필수 열입니다.

