---
title: geo_line_densify ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_line_densify ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: c5a66255f719d3bd0da962a8eb9d3cae23a8c254
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347836"
---
# <a name="geo_line_densify"></a>geo_line_densify()

중간 요소를 추가 하 여 평면 선 가장자리를 geodesics으로 변환 합니다.

## <a name="syntax"></a>Syntax

`geo_line_densify(`*lineString* `, ` *허용 오차*`)`

## <a name="arguments"></a>인수

* *lineString*: [GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 줄입니다.
* *허용 오차*: 원래 평면 가장자리와 변환 된 측 지 edge 체인 사이의 최대 거리 (미터)를 정의 하는 선택적 숫자입니다. 지원 되는 값은 [0.1, 1만] 범위에 있습니다. 지정 하지 않으면 기본값이 `10` 사용 됩니다.

## <a name="returns"></a>반환

[GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 Densified 선입니다. 줄 또는 허용 오차가 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> * 지리 공간적 좌표는 [Wgs-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 좌표 참조 시스템으로 표현 되는 것으로 해석 됩니다.

**LineString 정의**

dynamic ({"type": "LineString", "좌표계": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* LineString 좌표 배열에는 두 개 이상의 항목이 포함 되어야 합니다.
* [경도, 위도] 좌표는 유효 해야 합니다. 경도는 [-180, + 180] 범위의 실수 여야 하 고, 위도는 [-90, + 90] 범위의 실수 여야 합니다.
* 가장자리 길이는 180도 미만 이어야 합니다. 두 꼭 짓 점 사이의 최단 가장자리가 선택 됩니다.

**제약 조건**

* Densified 줄의 최대 요소 수는 10485760 개로 제한 됩니다.
* [동적](./scalar-data-types/dynamic.md) 형식으로 줄을 저장 하면 크기 제한이 있습니다.

**동기**

* [GeoJSON format](https://tools.ietf.org/html/rfc7946) 은 두 요소 사이의 가장자리를 일자형 직교 선으로 정의 합니다.
* 측 지 또는 평면 가장자리를 사용 하도록 결정 하는 것은 데이터 집합에 따라 달라질 수 있으며 특히 긴 가장자리와 관련이 있습니다.

## <a name="examples"></a>예제

다음 예에서는 맨해튼 아일랜드의도로를 densifies 합니다. 가장자리가 짧고, 평면 가장자리와 해당 측 지의 거리는 허용 오차로 지정 된 거리 보다 짧습니다. 따라서 결과는 변경 되지 않고 그대로 유지 됩니다.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"type": "LineString", "좌표": [[-73.949247, 40.796860], [-73.973017, 40.764323]]}|

다음 예제에서는 ~ 130km length의 가장자리를 densifies 합니다.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"type": "LineString", "좌표": [[50, 50], [50.125, 50.125], [50.25, 50.25], [50.375, 50.375], [50.5, 50.5], [50.625, 50.625], [50.75, 50.75], [50.875, 50.875], [51, 51]]}|

다음 예에서는 잘못 된 좌표 입력 때문에 null 결과를 반환 합니다.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

다음 예에서는 잘못 된 허용 오차 입력 때문에 null 결과를 반환 합니다.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
