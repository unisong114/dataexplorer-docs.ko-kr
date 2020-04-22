---
title: geo_distance_point_to_line() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_distance_point_to_line()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 34c6811ed5a51dae5281a4374421df5914c5b263
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663779"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

좌표와 지구상의 선 사이의 가장 짧은 거리를 계산합니다.

**구문**

`geo_distance_point_to_line(`*경도*`, `*위도*`, `*선스트링*`)`

**인수**

* *경도*: 지리 공간 좌표 경도 값을 도에서 조정합니다. 유효한 값은 실제 숫자이며 범위 [-180, +180]입니다.
* *위도*: 지리 공간 좌표 위도 도값입니다. 유효한 값은 실제 숫자이며 범위 [-90, +90]입니다.
* *lineString*: [GeoJSON 형식과](https://tools.ietf.org/html/rfc7946) [동적](./scalar-data-types/dynamic.md) 데이터 형식의 줄입니다.

**반환**

지구상의 좌표와 선 사이의 가장 짧은 거리(미터)입니다. 좌표 또는 lineString이 유효하지 않은 경우 쿼리는 null 결과를 생성합니다.

> [!NOTE]
> * 지리 공간 좌표는 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 계로 표현된 것으로 해석됩니다.
> * 지구상의 거리를 측정하는 데 사용되는 [측지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다. 선 모서리는 구의 측지선입니다.

**라인스트링 정의 및 구속조건**

동적({"유형": "LineString"," 좌표": [[lng_1,lat_1], [lng_2,lat_2] ,..., [lng_N,lat_N]]})

* LineString 좌표 배열에는 두 개 이상의 항목이 포함되어야 합니다.
* 좌표 [경도, 위도]는 경도가 [-180, +180] 범위의 실제 숫자이고 위도는 범위 [-90, +90]의 실제 숫자인 경우 유효해야 합니다.
* 모서리 길이는 180도 미만이어야 합니다. 두 정점 사이의 가장 짧은 모서리가 선택됩니다.

> [!TIP]
> 성능을 향상시면 리터럴 라인을 사용하십시오.

**예**

다음 예제는 노스 라스베이거스 공항과 인근 도로 사이의 가장 짧은 거리를 찾습니다.

:::image type="content" source="images/queries/geo/distance_point_to_line.png" alt-text="노스 라스베이거스 공항과 도로 사이의 거리":::

```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

미국 남부 해안에서 발생한 폭풍 사건. 이벤트는 정의된 해안선에서 최대 5km 떨어진 거리로 필터링됩니다.

:::image type="content" source="images/queries/geo/us_south_coast_storm_events.png" alt-text="미국 남부 해안의 폭풍 사건":::

```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

뉴욕 택시 픽업. 픽업은 정의된 선에서 0.1m의 최대 거리로 필터링됩니다.

:::image type="content" source="images/queries/geo/park_ave_ny_road.png" alt-text="미국 남부 해안의 폭풍 사건":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예제에서는 잘못된 LineString 입력으로 인해 null 결과를 반환합니다.
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

다음 예제에서는 잘못된 좌표 입력으로 인해 null 결과를 반환합니다.
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |