---
title: geo_point_to_geohash ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_point_to_geohash ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7998726637a7d19413954a509dd0ad9b34202f03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347768"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

지리적 위치에 대 한 geohash 문자열 값을 계산 합니다.

[Geohash](https://en.wikipedia.org/wiki/Geohash)에 대해 자세히 알아보세요.  

## <a name="syntax"></a>구문

`geo_point_to_geohash(`*경도* `, ` *위도* `, ` [*정확도*]`)`

## <a name="arguments"></a>인수

* *경도*: 지리적 위치의 경도 값입니다. X가 실수이 고 [-180, + 180] 범위에 있는 경우 경도 x는 유효한 것으로 간주 됩니다. 
* *위도*: 지리적 위치의 위도 값입니다. Y가 실수이 고 y가 [-90, + 90] 범위 내에 있는 경우 위도 y는 유효한 것으로 간주 됩니다. 
* *정확도*: 요청 된 `int` 정확도를 정의 하는 선택적입니다. 지원 되는 값은 [1, 18] 범위에 있습니다. 지정 하지 않으면 기본값이 `5` 사용 됩니다.

## <a name="returns"></a>반환

지정 된 지리적 위치의 geohash 문자열 값으로, 요청 된 정확도 길이입니다. 좌표 또는 정확성이 잘못 된 경우 쿼리에서 빈 결과가 생성 됩니다.

> [!NOTE]
>
> * Geohash는 유용한 지리 공간적 클러스터링 도구 일 수 있습니다.
> * Geohash에는 가장 낮은 수준 18에서 2500만 km²부터 0.6 μ ² 까지의 영역 범위가 있는 18 개의 정확도 수준이 있습니다.
> * Geohash의 일반적인 접두사는 서로의 지점의 유사성을 표시 합니다. 공유 접두사가 길수록 두 위치에 가까울수록 좋습니다. 정확도 값은 geohash length로 변환 됩니다.
> * Geohash는 평면 표면의 사각형 영역입니다.
> * 경도 x와 위도 y에서 계산 된 geohash 문자열에 대해 [geo_geohash_to_central_point ()](geo-geohash-to-central-point-function.md) 함수를 호출 하는 것은 x 및 y를 반환할 필요가 없습니다.
> * Geohash 정의로 인해 두 지리적 위치가 서로 매우 가까이 있지만 geohash 코드가 서로 다를 수 있습니다.

**정확도 값 당 geohash 사각형 영역 범위:**

| 정확도 | 너비     | 높이    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156.25 km | 156.25 km |
| 4        | 39.06 km  | 19.53 km  |
| 5        | 4.88 km   | 4.88 km   |
| 6        | 1.22 km   | 0.61 km   |
| 7        | 152.59 m  | 152.59 m  |
| 8        | 38.15 m   | 19.07 m   |
| 9        | 4.77 m    | 4.77 m    |
| 10       | 1.19 m    | 0.59 m    |
| 11       | 149.01 mm | 149.01 mm |
| 12       | 37.25 mm  | 18.63 mm  |
| 13       | 4.66 mm   | 4.66 mm   |
| 14       | 1.16 mm   | 0.58 mm   |
| 15       | 145.52 μ  | 145.52 μ  |
| 16       | 36.28 μ   | 18.19 μ   |
| 17       | 4.55 μ    | 4.55 μ    |
| 18       | 1.14 μ    | 0.57 μ    |

[Geo_point_to_s2cell ()](geo-point-to-s2cell-function.md)도 참조 하세요.

## <a name="examples"></a>예

Geohash로 집계 된 US 스톰 이벤트입니다.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="US geohash":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash|
|---|
|dhwfz15h|

다음 예에서는 좌표 그룹을 찾습니다. 그룹의 모든 좌표 쌍은 4.88 km x 4.88 km 사각형 영역에 상주 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| geohash | count | 위치  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

다음 예에서는 잘못 된 좌표 입력 때문에 빈 결과를 생성 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash |
|---------|
|         |

다음 예에서는 잘못 된 정확도 입력 때문에 빈 결과를 생성 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash |
|---------|
|         |
