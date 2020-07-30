---
title: geo_distance_point_to_line ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_distance_point_to_line ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: a7796c14098f773b73bd16735a3d2c9c879c8fd2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347870"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

좌표와 지구 선 사이의 최단 거리를 계산 합니다.

## <a name="syntax"></a>Syntax

`geo_distance_point_to_line(`*경도* `, ` *위도* `, ` *lineString*`)`

## <a name="arguments"></a>인수

* *경도*: 지리 공간적 좌표 경도 값 (도)입니다. 유효한 값은 [-180, + 180] 범위의 실수입니다.
* *위도*: 지리 공간적 좌표 위도 값 (도)입니다. 유효한 값은 [-90, + 90] 범위의 실수입니다.
* *lineString*: [GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 줄입니다.

## <a name="returns"></a>반환

좌표와 지구 선 사이의 최단 거리 (미터)입니다. 좌표 또는 lineString이 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> * 지리 공간적 좌표는 [Wgs-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 시스템으로 표현 되는 것으로 해석 됩니다.
> * 지구 거리를 측정 하는 데 사용 되는 [측 지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다. 구에는 선 가장자리가 [geodesics](https://en.wikipedia.org/wiki/Geodesic) 됩니다.
> * 입력 선 가장자리가 직선이 면 [geo_line_densify ()](geo-line-densify-function.md) 를 사용 하 여 평면 가장자리를 geodesics으로 변환 하는 것이 좋습니다.

**LineString 정의 및 제약 조건**

dynamic ({"type": "LineString", "좌표계": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* LineString 좌표 배열에는 두 개 이상의 항목이 포함 되어야 합니다.
* [경도, 위도] 좌표는 유효한 값 이어야 합니다. 여기서 경도는 [-180, + 180] 범위의 실수이 고, 위도는 [-90, + 90] 범위의 실수입니다.
* 가장자리 길이는 180도 미만 이어야 합니다. 두 꼭 짓 점 사이의 최단 가장자리가 선택 됩니다.

> [!TIP]
> 성능 향상을 위해 리터럴 줄을 사용 합니다.

## <a name="examples"></a>예제

다음 예에서는 북쪽 Las Vegas 공항과 가까운도로의 최단 거리를 찾습니다.

:::image type="content" source="images/geo-distance-point-to-line-function/distance-point-to-line.png" alt-text="북쪽 Las Vegas 공항 및도로 간의 거리":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

남부 해안의 스톰 이벤트입니다. 이벤트는 정의 된 줄에서 최대 5km 거리 만큼 필터링 됩니다.

:::image type="content" source="images/geo-distance-point-to-line-function/us-south-coast-storm-events.png" alt-text="미국 남부 해안의 스톰 이벤트":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Taxi pickups. Pickups은 정의 된 줄에서 최대 0.1 m 까지의 거리 만큼 필터링 됩니다.

:::image type="content" source="images/geo-distance-point-to-line-function/park-ave-ny-road.png" alt-text="미국 남부 해안의 스톰 이벤트":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예에서는 잘못 된 LineString 입력으로 인해 null 결과가 반환 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

다음 예에서는 잘못 된 좌표 입력 때문에 null 결과가 반환 됩니다.

```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |
