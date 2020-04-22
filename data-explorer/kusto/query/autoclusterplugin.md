---
title: 자동 클러스터 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 자동 클러스터 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bbc89f8214b5d64cdc605d1771da7c27064cc629
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663896"
---
# <a name="autocluster-plugin"></a>자동 클러스터 플러그인

```kusto
T | evaluate autocluster()
```

AutoCluster는 데이터에 나타나는 불연속 특성(차원)의 일반적인 패턴을 찾은 후 원래 쿼리의 결과(100개 행 또는 100,000개 행)를 더 작은 수의 패턴으로 줄입니다. AutoCluster는 실패(예: 예외, 작동 중단)를 분석하는 데 도움을 주기 위해 개발되었으나 필터링된 모든 데이터 집합에 작동할 수 있습니다. 

**구문**

`T | evaluate autocluster(`*인수*`)`

**반환**

AutoCluster는 여러 불연속 특성 간에 공통된 값을 공유하는 데이터 부분을 캡처하는 일반적으로 작은 패턴 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다. 

첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처된 원래 쿼리 에서 행의 개수와 백분율입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 와일드카드 값(기본적으로 null이며, 변수 값을 의미함)입니다. 

패턴은 분리되어 있지 않습니다. 겹칠 수도 있으며 일반적으로 모든 원본 행을 포함하지는 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

> [!TIP]
> 입력 파이프의 [위치와 투영을](./whereoperator.md) 사용하여 관심 있는 데이터로 데이터를 줄입니다. [project](./projectoperator.md)
>
> 관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

**인수(모두 선택 사항)**

`T | evaluate autocluster(`[*크기 무게,* *무게 열,* *NumSeeds,* *사용자 정의와일드 카드,* *사용자 정의 와일드 카드*, ...]`)`

모든 인수는 선택 사항이지만, 위와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

사용 가능한 인수:

* SizeWeight - 0 < *더블* < 1 [기본값: 0.5]

    제네릭(높은 적용 범위) 및 정보(많은 공유 값) 사이의 균형을 조절할 수 있습니다. 일반적으로 이 값을 늘리면 패턴 수가 줄어들고 각 패턴은 더 큰 비율에 적용되게 됩니다. 일반적으로 이 값을 줄이면 공유 값은 더 많고 비율 적용 범위는 더 작으며 더욱 구체적인 패턴이 생성됩니다. 후드 수식 아래에는 정규화된 일반 점수와 가중치와 함께 및 `SizeWeight` `1-SizeWeight` 가중치로 의한 유익한 점수 사이의 가중치 가중 기하학적 평균입니다. 

    예: `T | evaluate autocluster(0.8)`

* WeightColumn - *column_name*

    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
    
    예: `T | evaluate autocluster('~', sample_Count)` 

* NumSeeds - *int* [기본값: 25] 

    시드 수는 알고리즘의 초기 로컬 검색 지점의 수를 결정합니다. 일부 경우 데이터의 구조에 따라, 시드 수를 늘리면 증가된 검색 공간을 통해 쿼리는 느려지지만 결과의 수(또는 품질)가 증가합니다. 이 값은 양방향으로 결과가 감소하므로 5 미만으로 줄이면 성능이 저하되고 50 이상으로 증가하면 추가 패턴이 거의 생성되지 않습니다.

    예제: `T | evaluate autocluster('~', '~', 15)`

* 사용자 정의와일드 카드 - *"any_value_per_type"*

    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다.
    기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터에서 실행 가능한 값인 경우 다른 와일드카드 값(예: `*`)을 사용해야 합니다.

    예: `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**예제**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|개수|백분율|시스템 상태|EventType|피해|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7||우박|아니요
|1|512|8.7||뇌우를 동반한 바람|YES
|2|898|15.3|텍사스||

**사용자 지정 와일드카드가 사용된 예**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|개수|백분율|시스템 상태|EventType|피해|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7|\*|우박|아니요
|1|512|8.7|\*|뇌우를 동반한 바람|YES
|2|898|15.3|텍사스|\*|\*

**참고 항목**

* [basket](./basketplugin.md)
* [줄일](./reduceoperator.md)

**추가 정보**

* AutoCluster는 주로 [Algorithms for Telemetry Data Mining using Discrete Attributes](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)(불연속 특성을 사용한 원격 분석 데이터 마이닝을 위한 알고리즘) 논문에서 가져온 시드-확장 알고리즘을 기반으로 합니다. 

