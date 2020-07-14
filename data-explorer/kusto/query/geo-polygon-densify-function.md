---
title: geo_polygon_densify ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_polygon_densify ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 6ef78d3078fc396d4ebfb782e54f31096a1e8777
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280727"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify ()

중간 요소를 추가 하 여 polygon 또는 multipolygon 평면 가장자리를 geodesics으로 변환 합니다.

**구문**

`geo_polygon_densify(`*다각형* `, ` *허용 오차*`)`

**인수**

* *polygon*: [GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 다각형 또는 multipolygon입니다.
* *허용 오차*: 원래 평면 가장자리와 변환 된 측 지 edge 체인 사이의 최대 거리 (미터)를 정의 하는 선택적 숫자입니다. 지원 되는 값은 [0.1, 1만] 범위에 있습니다. 지정 하지 않으면 기본값이 `10` 사용 됩니다.

**반환**

[GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 다각형을 Densified 합니다. 다각형이 나 허용 오차 중 하나가 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> * 지리 공간적 좌표는 [Wgs-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 시스템으로 표현 되는 것으로 해석 됩니다.
> * 다각형은 올바르게 정의 되어야 하지만 함수는 polygon 유효성을 검사 하지 않습니다.

**Polygon 정의**

dynamic ({"type": "Polygon", "좌표": [LinearRingShell, LinearRingHole_1,..., LinearRingHole_N]})

동적 ({"type": "MultiPolygon", "좌표": [[LinearRingShell, LinearRingHole_1,..., LinearRingHole_N],..., [LinearRingShell, LinearRingHole_1,...,]]})

* LinearRingShell는 필수 이며 `counterclockwise` , [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]의 정렬 된 배열로 정의 됩니다. 셸이 하나만 있을 수 있습니다.
* LinearRingHole은 선택 사항이 며,..., [ `clockwise` [lng_1, lat_1] [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]의 정렬 된 배열로 정의 됩니다. 임의 개수의 내부 링 및 구멍이 있을 수 있습니다.
* LinearRing 꼭 짓 점은 세 개 이상의 좌표와 구분 되어야 합니다. 첫 번째 좌표는 마지막 좌표와 같아야 합니다. 최소 4 개의 항목이 필요 합니다.
* [경도, 위도] 좌표는 유효 해야 합니다. 경도는 [-180, + 180] 범위의 실수 여야 하 고 위도는 [-90, + 90] 범위의 실수 여야 합니다.
* LinearRingShell는 구의 절반을 포함 합니다. LinearRing는 구를 두 영역으로 나눕니다. 두 지역 중 작은 값이 선택 됩니다.
* LinearRing edge 길이는 180도 미만 이어야 합니다. 두 꼭 짓 점 사이의 최단 가장자리가 선택 됩니다.

**제약 조건**

* Densified polygon의 최대 요소 수는 10485760 개로 제한 됩니다.
* 다각형을 [동적](./scalar-data-types/dynamic.md) 형식으로 저장 하면 크기 제한이 있습니다.
* 유효한 다각형이 Densifying 무효화 될 수 있습니다. 알고리즘은 균일 하지 않은 방식으로 요소를 추가 하 여 가장자리가 서로 상호 작용 하도록 할 수 있습니다.

**동기**

* [GeoJSON format](https://tools.ietf.org/html/rfc7946) 은 두 요소 사이의 가장자리를 일자형 직교 선으로 정의 합니다.
* 측 지 또는 평면 가장자리를 사용 하도록 결정 하는 것은 데이터 집합에 따라 달라질 수 있으며 특히 긴 가장자리와 관련이 있습니다.

**예**

다음 예제에서는 densifies 맨해튼 Central 공원 polygon을 말합니다. 가장자리는 짧고 평면 가장자리와 해당 측 지 된 항목 간의 거리가 허용 오차로 지정 된 거리 보다 짧습니다. 따라서 결과는 변경 되지 않고 그대로 유지 됩니다.

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"type": "Polygon", "좌표": [[[-73.958244, 40.800719], [-73.949146, 40.79695], [-73.973093, 40.764226], [-73.982062, 40.768159], [-73.958244, 40.800719]]]}|

다음 예제에서는 다각형의 가장자리 두 개를 densifies 합니다. Densified 가장자리 길이는 ~ 110 km입니다.

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"type": "Polygon", "좌표": [[10, 10], [10.25, 10], [10.5, 10], [10.75, 10], [11, 10], [11, 11], [10.75, 11], [10.5, 11], [10.25, 11], [10, 11], [10, 10]]]}|

다음 예에서는 잘못 된 좌표 입력 때문에 null 결과를 반환 합니다.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

다음 예에서는 잘못 된 허용 오차 입력 때문에 null 결과를 반환 합니다.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
