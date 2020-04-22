---
title: geo_distance_2points() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_distance_2points()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 4b63ffaee8123b4ee281426f30f757fbe5165e75
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663715"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

지구상의 두 지리 공간 좌표 사이의 가장 짧은 거리를 계산합니다.

**구문**

`geo_distance_2points(`*p1_longitude*`, `*p1_latitude*`, `*p2_latitude* *p2_longitude*`, ``)`

**인수**

* *p1_longitude*: 첫 번째 지리 공간 좌표, 경도 값도. 유효한 값은 실제 숫자이며 범위 [-180, +180]입니다.
* *p1_latitude*: 첫 번째 지리 공간 좌표, 위도 값도. 유효한 값은 실제 숫자이며 범위 [-90, +90]입니다.
* *p2_longitude*: 두 번째 지리 공간 좌표, 경도 값도. 유효한 값은 실제 숫자이며 범위 [-180, +180]입니다.
* *p2_latitude*: 두 번째 지리 공간 좌표, 위도 값도. 유효한 값은 실제 숫자이며 범위 [-90, +90]입니다.

**반환**

지구상의 두 지리적 위치 사이의 가장 짧은 거리(미터)입니다. 좌표가 유효하지 않으면 쿼리에서 null 결과를 생성합니다.

> [!NOTE]
> * 지리 공간 좌표는 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 계로 표현된 것으로 해석됩니다.
> * 지구상의 거리를 측정하는 데 사용되는 [측지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다.

**예**

다음 예제는 시애틀과 로스앤젤레스 간 가장 짧은 거리를 찾습니다.

:::image type="content" source="images/queries/geo/distance_2points_seattle_los_angeles.png" alt-text="시애틀과 로스앤젤레스 사이의 거리":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

다음은 시애틀에서 런던까지의 가장 짧은 경로에 대한 근사치입니다. 선은 LineString을 따라 500미터 이내의 좌표로 구성됩니다.

:::image type="content" source="images/queries/geo/line_seattle_london.png" alt-text="시애틀 - 런던 라인스트링":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예제에서는 두 좌표 사이의 가장 짧은 거리가 1~11미터 사이인 모든 행을 찾습니다.
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

다음 예제에서는 잘못된 좌표 입력으로 인해 null 결과를 반환합니다.
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |