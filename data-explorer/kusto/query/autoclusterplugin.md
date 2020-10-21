---
title: autocluster 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 autocluster 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a7caa8935176b2d4d52bf8955262509bb2069dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248061"
---
# <a name="autocluster-plugin"></a>autocluster 플러그 인

```kusto
T | evaluate autocluster()
```

`autocluster` 데이터에서 불연속 특성 (차원)의 일반적인 패턴을 찾습니다. 그런 다음 원래 쿼리 결과를 100 또는 100,000 개 행이 든 적은 수의 패턴으로 줄입니다. 플러그 인은 오류 (예: 예외 또는 충돌)를 분석 하는 데 도움이 되도록 개발 되었지만 모든 필터링 된 데이터 집합에서 작동할 수 있습니다.

> [!NOTE]
> `autocluster` 는 주로 [불연속 특성을 사용 하 여 원격 분석 데이터 마이닝에 대 한 알고리즘](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)의 Seed-Expand 알고리즘을 기반으로 합니다. 


## <a name="syntax"></a>구문

`T | evaluate autocluster(`*인수*`)`

## <a name="returns"></a>반환

`autocluster`플러그 인은 일반적으로 작은 패턴 집합을 반환 합니다. 패턴은 여러 불연속 특성에서 공유 공통 값을 사용 하 여 데이터의 일부를 캡처합니다. 결과의 각 패턴은 행으로 표시 됩니다.

첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처되는 원래 쿼리 중에서 행의 개수 및 백분율입니다. 나머지 열은 원래 쿼리에서 가져옵니다. 해당 값은 열의 특정 값 이거나, 와일드 카드 값 (기본적으로 null 임)으로, 변수 값을 의미 합니다.

패턴은 고유 하지 않으며 겹칠 수 있으며 일반적으로 모든 원본 행을 포함 하지 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

> [!TIP]
> 입력 파이프에 [where](./whereoperator.md) 와 [project](./projectoperator.md) 를 사용 하 여 데이터를 원하는 것 으로만 줄입니다.
>
> 관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

## <a name="arguments"></a>인수 

> [!NOTE] 
> 모든 인수는 선택 사항입니다.

`T | evaluate autocluster(`[*Sizeweight*, *WeightColumn*, *numseeds*, *customwildcard 카드*, *customwildcard 카드*, ...]`)`

모든 인수는 선택 사항이지만, 위와 같은 순서여야 합니다. 기본값을 사용 해야 함을 나타내려면 물결표 값 ' ~ ' 문자열을 입력 합니다 (테이블의 "예제" 열 참조).

|인수        | 유형, 범위, 기본값              |설명                | 예제                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| 적용     | 0 < *double* < 1 [기본값: 0.5]   | 에서는 제네릭 (높은 범위) 및 정보 (많은 공유) 값 간의 균형을 제어할 수 있습니다. 값을 늘리면 일반적으로 패턴 수가 줄어들고 각 패턴은 더 큰 비율의 적용을 포함 하는 경향이 있습니다. 값을 줄이면 일반적으로 더 많은 공유 값이 포함 된 더 구체적인 패턴과 더 작은 비율을 생성 합니다. -후드 수식은 정규화 된 일반 점수와 가중치를 사용 하는 정보 점수 사이의 가중치가 적용 된 기 하 도형입니다. `SizeWeight``1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | 지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 인수는 숫자 열의 이름 이어야 합니다 (예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [기본값: 25]              | 시드 수는 알고리즘의 초기 로컬 검색 지점의 수를 결정합니다. 경우에 따라 데이터의 구조에 따라 초기값 수를 늘리면 확장 된 검색 공간을 통해 결과의 수 나 품질이 증가 하 여 쿼리 균형을 저하 시킬 수 있습니다. 이 값의 결과는 양쪽 방향으로 축소 되므로 5 미만으로 줄이면 성능이 크게 향상 됩니다. 50 이상으로 늘리면 추가 패턴이 거의 생성 되지 않습니다.                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard 카드  | *"any_value_per_type"*           | 결과 테이블의 특정 형식에 대 한 와일드 카드 값을 설정 합니다. 현재 패턴에이 열에 대 한 제한이 없음을 표시 합니다. 기본값은 null입니다. 문자열 기본값은 빈 문자열 이기 때문입니다. 기본값이 데이터에서 양호한 값 이면 다른 와일드 카드 값 (예:)을 사용 해야 합니다 `*` .                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>예

### <a name="using-autocluster"></a>Autocluster 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="using-custom-wildcards"></a>사용자 지정 와일드 카드 사용

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

## <a name="see-also"></a>참조

* [basket](./basketplugin.md)
* [줄이십시오](./reduceoperator.md)
