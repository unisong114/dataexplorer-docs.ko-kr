---
title: 바구니 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 바구니 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 06fd1e33624bca6aee18a1ca969af18656c6b3e0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663877"
---
# <a name="basket-plugin"></a>바구니 플러그인

```kusto
T | evaluate basket()
```

Basket은 데이터에서 자주 나오는 모든 불연속 특성(차원) 패턴을 찾고, 원본 쿼리의 빈도 임계값을 통과한 자주 나오는 모든 패턴을 반환합니다. 바구니는 데이터에서 모든 빈번한 패턴을 찾을 수 있지만 다항식 런타임이 보장되지는 않지만 쿼리의 런타임은 행 수에 선형이지만 경우에 따라 열 수(차원)가 기하급수적일 수 있습니다. Basket은 바구니 분석 데이터 마이닝용으로 개발된 Apriori 알고리즘을 기준으로 합니다.

**구문**

`T | evaluate basket(`*인수*`)`

**반환**

바구니는 행의 비율 임계값(기본값: 0.05) 위에 나타나는 모든 빈번한 패턴을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다.

첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처된 원래 쿼리 에서 행의 개수와 백분율입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 와일드카드 값(기본적으로 null이며, 변수 값을 의미함)입니다.

**인수(모두 선택 사항)**

`T | evaluate basket(`[*임계값*, *가중치 열,* *최대 치수,* 사용자 *정의와일드 카드,* *사용자 정의 와일드 카드*, ...]`)`

모든 인수는 선택 사항이지만, 위와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

사용 가능한 인수:

* 임계값 - 0.015 < *더블* < 1 [기본값: 0.05]

    자주 나오는 것으로 간주되는 행의 최소 비율을 설정합니다(비율이 더 작은 패턴은 반환되지 않음).
    
    예: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
    
    예: `T | evaluate basket('~', sample_Count)`

* 최대치 - 1 < *int* [기본값: 5]

    쿼리 런타임의 줄이기 위해 기본적으로 제한되는 바구니당 상관 관계가 없는 차원의 최대 수를 설정합니다.

    예: `T | evaluate basket('~', '~', 3)`

* 사용자 정의와일드 카드 - *"any_value_per_type"*

    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다.
    기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터에서 실행 가능한 값인 경우 다른 와일드카드 값(예: `*`)을 사용해야 합니다.
    아래 예제를 참조하세요.

    예: `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**예제**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|개수|백분율|시스템 상태|EventType|피해|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|||아니요|0
|1|2278|38.7||우박|아니요|0
|2|5675|96.4||||0
|3|2371|40.3||우박||0
|4|1279|21.7||뇌우를 동반한 바람||0
|5|2468|41.9||우박||
|6|1310|22.3|||YES|
|7|1291|21.9||뇌우를 동반한 바람||

**사용자 지정 와일드카드가 사용된 예**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|SegmentId|개수|백분율|시스템 상태|EventType|피해|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|\*|\*|아니요|0
|1|2278|38.7|\*|우박|아니요|0
|2|5675|96.4|\*|\*|\*|0
|3|2371|40.3|\*|우박|\*|0
|4|1279|21.7|\*|뇌우를 동반한 바람|\*|0
|5|2468|41.9|\*|우박|\*|-1
|6|1310|22.3|\*|\*|YES|-1
|7|1291|21.9|\*|뇌우를 동반한 바람|\*|-1
