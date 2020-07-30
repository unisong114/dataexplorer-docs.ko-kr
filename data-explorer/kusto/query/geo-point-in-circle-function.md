---
title: geo_point_in_circle ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_point_in_circle ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 1e94e8eca72e6cb679a84e7b91ea376b9ec4b29c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347819"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

지리 공간적 좌표가 지구에 원 안에 있는지 여부를 계산 합니다.

## <a name="syntax"></a>Syntax

`geo_point_in_circle(`*p_longitude* `, ` *p_latitude* `, ` *pc_longitude* `, ` *pc_latitude* `, ` *c_radius*`)`

## <a name="arguments"></a>인수

* *p_longitude*: 지리 공간적 좌표 경도 값 (도)입니다. 유효한 값은 [-180, + 180] 범위의 실수입니다.
* *p_latitude*: 지리 공간적 좌표 위도 값 (도)입니다. 유효한 값은 [-90, + 90] 범위의 실수입니다.
* *pc_longitude*: 원 중심 지리 공간적 좌표 경도 값 (도)입니다. 유효한 값은 [-180, + 180] 범위의 실수입니다.
* *pc_latitude*: 원 중심 지리 공간적 좌표 위도 값 (도)입니다. 유효한 값은 [-90, + 90] 범위의 실수입니다.
* *c_radius*: 미터 단위의 원 반지름입니다. 유효한 값은 양수 여야 합니다.

## <a name="returns"></a>반환

지리 공간적 좌표가 원 내에 있는지 여부를 나타냅니다. 좌표가 나 원이 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
>* 지리 공간적 좌표는 [Wgs-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 시스템으로 표현 되는 것으로 해석 됩니다.
>* 지구 거리를 측정 하는 데 사용 되는 [측 지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다.
>* 원은 지구에서 구형 캡입니다. 캡의 반지름은 구의 표면에 따라 측정 됩니다.

## <a name="examples"></a>예제

다음 쿼리는 다음 원으로 정의 된 영역에 있는 모든 위치를 찾습니다. 예를 들어 반경 18 km, 가운데 맞춤 [-122.317404, 47.609119] 좌표입니다.

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="시애틀 근처에 배치":::

<!-- csl: https://help.kusto.windows.net/Samples -->
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

올랜도의 스톰 이벤트입니다. 이벤트는 올랜도 좌표 내에서 100 km으로 필터링 되 고 이벤트 유형 및 해시로 집계 됩니다.

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="올랜도의 스톰 이벤트":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예제에서는 특정 위치의 10 미터 이내에 있는 Taxi pickups를 보여 줍니다. 관련 pickups는 해시에 의해 집계 됩니다.

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="Pickups 근처의 Taxi":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

다음 예에서는 true를 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

다음 예에서는 false를 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

다음 예에서는 잘못 된 좌표 입력 때문에 null 결과가 반환 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

다음 예제에서는 잘못 된 원 반경 입력 때문에 null 결과를 반환 합니다.

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
