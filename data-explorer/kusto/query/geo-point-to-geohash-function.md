---
title: geo_point_to_geohash() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_point_to_geohash()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030119"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

지리적 위치에 대한 Geohash 문자열 값을 계산합니다.

지오해시에 대한 자세한 내용은 [여기를](https://en.wikipedia.org/wiki/Geohash)참조하십시오.  

**구문**

`geo_point_to_geohash(`*경도*`, `*위도*`, `*[정확도]*`)`

**인수**

* *경도*: 지리적 위치의 경도 값입니다. 경도 x는 x가 실제 숫자이고 [-180, +180] 범위에 있는 경우 유효한 것으로 간주됩니다. 
* *위도*: 지리적 위치의 위도 값입니다. 위도 y는 y가 실제 숫자이고 y가 [-90, +90] 범위에 있는 경우 유효한 것으로 간주됩니다. 
* *정확도*: `int` 요청된 정확도를 정의하는 선택적 옵션입니다. 지원되는 값은 [1,18]의 범위에 있습니다. 지정되지 않은 경우 `5` 기본값이 사용됩니다.

**반환**

요청된 정확도 길이를 가진 지정된 지리적 위치의 Geohash 문자열 값입니다. 좌표 또는 정확도가 잘못되면 쿼리에 빈 결과가 생성됩니다.

> [!NOTE]
>
> * 지오해시는 유용한 지리 공간 클러스터링 도구가 될 수 있습니다.
> * Geohash는 가장 높은 레벨 1에서 0.6 μ²까지 2,500만 km²에 이르는 면적 커버리지를 가진 18개의 정확도 레벨을 가지고 있습니다 18.
> * 지오해스의 일반적인 접두사는 서로 점의 근접성을 나타냅니다. 공유 접두사가 길수록 두 장소가 더 가까워진다. 정확도 값은 지오해시 길이로 변환됩니다.
> * 지오해시는 평면 표면의 직사각형 영역입니다.
> * 경도 x 및 위도 y에서 계산된 geohash 문자열에서 [geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md) 함수를 호출하면 반드시 x와 y를 반환할 필요는 없습니다.
> * Geohash 정의로 인해 두 지리적 위치가 서로 매우 가깝지만 서로 다른 Geohash 코드가 있을 수 있습니다.

**정확도 값당 지오해시 직사각형 영역 커버리지:**

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

또한 [geo_point_to_s2cell()](geo-point-to-s2cell-function.md)을 참조하십시오.

**예**

지오해시에 의해 집계 된 미국 폭풍 이벤트.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="미국 지오해시":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| 지오 해시 ()(주)(주)      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|지오 해시 ()(주)(주)|
|---|
|dhwfz15h|

다음 예제는 좌표 그룹을 찾습니다. 그룹의 모든 좌표는 4.88km에 4.88km의 직사각형 영역에 상주합니다.
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

| 지오 해시 ()(주)(주) | count | 위치  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

다음 예제에서는 잘못된 좌표 입력으로 인해 빈 결과가 생성됩니다.
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| 지오 해시 ()(주)(주) |
|---------|
|         |

다음 예제에서는 잘못된 정확도 입력으로 인해 빈 결과가 생성됩니다.
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| 지오 해시 ()(주)(주) |
|---------|
|         |