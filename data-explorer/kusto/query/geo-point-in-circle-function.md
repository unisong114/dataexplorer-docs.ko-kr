---
title: geo_point_in_circle() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_point_in_circle()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663794"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

지리 공간 좌표가 지구의 원 안에 있는지 여부를 계산합니다.

**구문**

`geo_point_in_circle(`*c_radius pc_longitude p_longitude*`, `*p_latitude*`, `*pc_latitude pc_longitude*`, `*c_radius* *pc_longitude*`, ``)`

**인수**

* *p_longitude*: 지리 공간 좌표 경도 값을 도. 유효한 값은 실제 숫자이며 범위 [-180, +180]입니다.
* *p_latitude*: 지리 공간 좌표 위도 도값입니다. 유효한 값은 실제 숫자이며 범위 [-90, +90]입니다.
* *pc_longitude*: 원 중심 지리 공간 좌표 경도 값을 도에 따라 조정합니다. 유효한 값은 실제 숫자이며 범위 [-180, +180]입니다.
* *pc_latitude*: 원 중심 지리 공간 좌표 위도 도값입니다. 유효한 값은 실제 숫자이며 범위 [-90, +90]입니다.
* *c_radius*: 미터의 원 반지름. 유효한 값은 양수여야 합니다.

**반환**

지리 공간 좌표가 원 안에 있는지 여부를 나타냅니다. 좌표 또는 원이 유효하지 않은 경우 쿼리는 null 결과를 생성합니다.

> [!NOTE]
>* 지리 공간 좌표는 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 계로 표현된 것으로 해석됩니다.
>* 지구상의 거리를 측정하는 데 사용되는 [측지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다.
>* 원은 지구의 구형 캡입니다. 캡의 반지름은 구의 표면을 따라 측정됩니다.

**예**

다음 쿼리는 중심이 [-122.317404, 47.609119] 좌표에 있는 반지름이 18km인 원으로 정의된 영역의 모든 장소를 찾습니다.

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="시애틀 근처 장소":::

```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|Kirkland|
|Redmond|

올랜도에서 폭풍 이벤트. 이벤트는 올랜도 좌표에 의해 필터링되고 100km 이내이며 이벤트 유형 및 해시별로 집계됩니다.

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="올랜도의 폭풍 사건":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예제에서는 일부 위치와 10미터 이내의 거리에 있는 NY 택시 픽업을 보여 주었습니다. 관련 픽업은 해시로 집계됩니다.

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="뉴욕 택시 근처 픽업":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

다음 예제는 true를 반환합니다.
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

다음 예제는 false를 반환합니다.
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

다음 예제에서는 잘못된 좌표 입력으로 인해 null 결과를 반환합니다.
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

다음 예제에서는 잘못된 원 반지름 입력으로 인해 null 결과를 반환합니다.
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||