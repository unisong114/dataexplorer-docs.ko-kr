---
title: geo_geohash_to_central_point ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_geohash_to_central_point ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: eb59eae0bc014c6ce9060d65f6c3aced80e4275c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227131"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Geohash 사각형 영역의 중심을 나타내는 지리 공간적 좌표를 계산 합니다.

에 대해 자세히 알아보세요 [`geohash`](https://en.wikipedia.org/wiki/Geohash) .  

**구문**

`geo_geohash_to_central_point(`*geohash*`)`

**인수**

*geohash*: geohash 문자열 값 [geo_point_to_geohash ()](geo-point-to-geohash-function.md)에서 계산 된 값입니다. Geohash 문자열은 1 ~ 18 자까지 사용할 수 있습니다.

**반환**

[GeoJSON Format](https://tools.ietf.org/html/rfc7946) 및 [dynamic](./scalar-data-types/dynamic.md) data 형식의 지리 공간적 좌표 값입니다. Geohash가 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> GeoJSON 형식은 경도 first와 위도 second를 지정 합니다.

**예**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|좌표|longitude|latitude|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "좌표": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

다음 예에서는 잘못 된 geohash input 때문에 null 결과를 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>예: 위치 만들기-Bing Maps 링크

Geohash 값을 사용 하 여 geohash 중심점을 가리켜 Bing Maps에 대 한 딥 링크 URL을 만들 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
