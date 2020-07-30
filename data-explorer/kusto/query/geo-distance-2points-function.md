---
title: geo_distance_2points ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_distance_2points ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 0e8d57e76b0dfa45003f541b54360cb4ac414646
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347887"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

지구에서 두 지리 공간적 좌표 사이의 최단 거리를 계산 합니다.

## <a name="syntax"></a>구문

`geo_distance_2points(`*p1_longitude* `, ` *p1_latitude* `, ` *p2_longitude* `, ` *p2_latitude*`)`

## <a name="arguments"></a>인수

* *p1_longitude*: 첫 번째 지리 공간적 좌표, 경도 값 (도)입니다. 유효한 값은 [-180, + 180] 범위의 실수입니다.
* *p1_latitude*: 첫 번째 지리 공간적 좌표, 위도 값 (도)입니다. 유효한 값은 [-90, + 90] 범위의 실수입니다.
* *p2_longitude*: 두 번째 지리 공간적 좌표, 경도 값 (도)입니다. 유효한 값은 [-180, + 180] 범위의 실수입니다.
* *p2_latitude*: 두 번째 지리 공간적 좌표 이며 위도 값 (도)입니다. 유효한 값은 [-90, + 90] 범위의 실수입니다.

## <a name="returns"></a>반환

지구에서 두 지리적 위치 사이의 최단 거리 (미터)입니다. 좌표가 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> * 지리 공간적 좌표는 [Wgs-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 시스템으로 표현 되는 것으로 해석 됩니다.
> * 지구 거리를 측정 하는 데 사용 되는 [측 지 데이텀은](https://en.wikipedia.org/wiki/Geodetic_datum) 구입니다.

## <a name="examples"></a>예

다음 예에서는 시애틀에서 로스앤젤레스 사이의 최단 거리를 찾습니다.

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="시애틀와 로스앤젤레스 간 거리":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

시애틀에서 런던 까지의 최단 경로에 대 한 근사치는 다음과 같습니다. 선은 LineString의 좌표와 500 미터 이내로 구성 됩니다.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="시애틀에서 런던으로 LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

다음 예에서는 두 좌표 사이의 최단 거리가 1에서 11 미터 사이인 모든 행을 찾습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

다음 예에서는 잘못 된 좌표 입력 때문에 null 결과를 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
