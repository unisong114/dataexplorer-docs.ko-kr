---
title: 바구니 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 바구니 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: d95bed91ab07be3feebecffbb97378866cb7c6c9
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197229"
---
# <a name="basket-plugin"></a>바구니 플러그 인

```kusto
T | evaluate basket()
```

바구니는 데이터에서 불연속 특성 (차원)의 모든 빈번한 패턴을 찾습니다. 그런 다음 원래 쿼리에서 빈도 임계값을 통과 한 빈번한 패턴을 반환 합니다. 바구니는 데이터에서 자주 발생 하는 모든 패턴을 찾을 수 있지만 다항식 런타임이 보장 되지는 않습니다. 쿼리의 런타임은 행의 수에 선형 이지만 열 수 (차원)의 지 수를 지정할 수 있습니다. Basket은 바구니 분석 데이터 마이닝용으로 개발된 Apriori 알고리즘을 기준으로 합니다.

**구문**

`T | evaluate basket(`*인수*`)`

**반환**

바구니는 행의 비율 임계값 위에 나타나는 자주 나오는 모든 패턴을 반환 합니다. 기본 임계값은 0.05입니다. 각 패턴은 결과의 행으로 표시됩니다.

첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처되는 원래 쿼리에서 *행의* *개수* 및 백분율입니다. 나머지 열은 원래 쿼리에서 가져옵니다.
해당 값은 열의 특정 값 또는 와일드 카드 값입니다. 기본값은 null입니다 .이 값은 변수 값을 의미 합니다.

**인수(모두 선택 사항)**

`T | evaluate basket(`[*Threshold*, *WeightColumn*, *maxdimensions*, *customwildcard*카드, *customwildcard 카드*, ...]`)`

모든 인수는 선택 사항이지만, 위와 같은 순서여야 합니다. 기본값을 사용 해야 함을 나타내려면 물결표 값-' ~ ' 문자열을 사용 합니다. 아래 예제를 참조하세요.

사용 가능한 인수:

* 임계값-0.015 < *double* < 1 [기본값: 0.05]

    자주 고려 될 행의 최소 비율을 설정 합니다. 비율이 작은 패턴은 반환 되지 않습니다.
    
    예: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    지정 된 가중치에 따라 입력의 각 행을 고려 합니다. 기본적으로 각 행의 가중치는 ' 1 '입니다. 인수는 int, long, real 등의 숫자 열 이름 이어야 합니다. 가중치 열을 일반적으로 사용 하는 것은 각 행에 이미 포함 되어 있는 데이터의 버킷 팅/집계를 고려 하는 것입니다.

    예: `T | evaluate basket('~', sample_Count)`

* MaxDimensions-1 < *int* [기본값: 5]

    쿼리 런타임을 최소화 하기 위해 기본적으로 제한 된 바구니 당 상관 관계가 없는 차원의 최대 수를 설정 합니다.

    예: `T | evaluate basket('~', '~', 3)`

* CustomWildcard 카드- *"any_value_per_type"*

    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다.
    기본값은 null입니다. 문자열의 기본값은 빈 문자열입니다. 기본값이 데이터에서 양호한 값 이면와 같이 다른 와일드 카드 값을 사용 해야 합니다 `*` .

    예를 들어:

     `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
