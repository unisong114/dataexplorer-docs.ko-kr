---
title: 쿼리 모범 사례-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 모범 사례를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 301917f363176fb8e3bbf2fe4286a86b7a5674ea
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280520"
---
# <a name="query-best-practices"></a>쿼리 모범 사례

다음은 쿼리를 더 빠르게 실행 하기 위해 수행할 수 있는 몇 가지 모범 사례입니다.

|작업  |용도  |사용 안 함  |참고  |
|---------|---------|---------|---------|
| **시간 필터** | 먼저 시간 필터를 사용합니다. ||Kusto는 시간 필터를 사용 하도록 최적화 되어 있습니다.| 
|**문자열 연산자**      | 연산자 사용 `has`     | 사용 안 함`contains`     | 전체 토큰을 찾을 때는 `has` 부분 문자열을 찾을 수 없기 때문에 더 효율적으로 작동 합니다.   |
|**대/소문자 구분 연산자**     |  `==` 사용       | 사용 안 함`=~`       |  가능 하면 대/소문자 구분 연산자를 사용 합니다.       |
| | `in` 사용 | 사용 안 함`in~`|
|  | `contains_cs` 사용         | 사용 안 함`contains`        | 을 사용 `has` / `has_cs` 하 고 사용할 수 없는 경우 `contains` / `contains_cs` 훨씬 더 좋습니다. |
| **텍스트 검색**    |    특정 열을 찾습니다.     |    사용 안 함`*`    |   `*`모든 열에서 전체 텍스트 검색을 수행 합니다.    |
| **수백만 개의 행에서 [동적 개체](./scalar-data-types/dynamic.md) 의 필드 추출**    |  대부분의 쿼리가 수백만 개의 행에서 동적 개체의 필드를 추출 하는 경우 수집 시 열을 구체화 합니다.      |         | 이렇게 하면 열 추출에 대해 한 번만 지불 하면 됩니다.    |
| **`let`두 번 이상 사용 하는 값을 포함 하는 문** | [구체화 () 함수](./materializefunction.md) 사용 |  |   를 사용 하는 방법에 대 한 자세한 내용은 `materialize()` [구체화 ()](materializefunction.md)를 참조 하세요.|
| **10억 개를 초과 하는 레코드에 대 한 변환 적용**| 쿼리를 변형 하 여 변환에 공급 되는 데이터의 양을 줄입니다.| 피할 수 있는 경우에는 많은 양의 데이터를 변환 하지 마십시오. | |
| **새 쿼리** | `limit [small number]`끝에 또는를 사용 `count` 합니다. | |     알 수 없는 데이터 집합에 대해 바인딩되지 않은 쿼리를 실행 하는 경우 클라이언트에 반환 되는 결과의 Gb이 발생 하 여 응답 속도가 느려지고 사용 중인 클러스터가 생성 될 수 있습니다.|
| **대/소문자를 구분 하지 않는 비교** | `Col =~ "lowercasestring"` 사용 | 사용 안 함`tolower(Col) == "lowercasestring"` |
| **이미 소문자 또는 대문자로 되어 있는 데이터 비교** | `Col == "lowercasestring"`(또는 `Col == "UPPERCASESTRING"`) | 대/소문자를 구분 하지 않는 비교를 사용 하지 마세요.||
| **열 필터링** |  테이블 열에 대해 필터링 합니다.|계산 열에서 필터링 하지 않습니다. | |
| | `T | where predicate(<expression>)` 사용 | 사용 안 함`T | extend _value = <expression> | where predicate(_value)` ||
| **summarize 연산자** |  요약 연산자의가 높은 카디널리티를 사용 하는 경우 힌트를 사용 [합니다. 전략 = 순서 섞기](./shufflequery.md) `group by keys` | | 높은 카디널리티는 100만 이상으로 이상적입니다.|
|**[join 연산자](./joinoperator.md)** | 가장 작은 행이 있는 테이블을 선택 하 여 첫 번째 행 (가장 왼쪽에 있는 쿼리)을 선택 합니다. ||
| 클러스터 간 조인 |클러스터에서 대부분의 데이터가 있는 조인의 "right" 쪽에서 쿼리를 실행 합니다. ||
|왼쪽이 작고 오른쪽이 클 때 조인 | Use [힌트. 전략 = 브로드캐스트](./broadcastjoin.md) || Small는 최대 10만 개의 레코드를 나타냅니다. |
|양쪽 모두 너무 클 경우 조인 | Use [힌트. 전략 = 순서 섞기](./shufflequery.md) || 조인 키에 높은 카디널리티가 있는 경우를 사용 합니다.|
|**동일한 형식이 나 패턴을 공유 하는 문자열을 사용 하 여 열에서 값 추출**|  [Parse 연산자](./parseoperator.md) 사용 | 여러 문을 사용 하지 마세요 `extract()` .  | 예를 들면 다음과 같은 값입니다.`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[extract () 함수](./extractfunction.md)**| 구문 분석 된 문자열이 모두 동일한 형식이 나 패턴을 따르지 않는 경우를 사용 합니다.| |REGEX를 사용 하 여 필요한 값을 추출 합니다.|
| **[구체화 () 함수](./materializefunction.md)** | 구체화 된 데이터 집합을 줄이고 쿼리의 의미 체계를 유지 하는 모든 가능한 연산자를 푸시합니다. | |예를 들어, 필터 또는 project only는 필수 열입니다.
