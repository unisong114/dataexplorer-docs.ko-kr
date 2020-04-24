---
title: geo_geohash_to_central_point() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_geohash_to_central_point()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514640"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

지오해시 직사각형 영역의 중심을 나타내는 지리 공간 좌표를 계산합니다.

지오해시에 대한 자세한 내용은 [위키백과를](https://en.wikipedia.org/wiki/Geohash)참조하십시오.  

**구문**

`geo_geohash_to_central_point(`*지오 해시 ()(주)(주)*`)`

**인수**

*geohash*: [geo_point_to_geohash()](geo-point-to-geohash-function.md)으로 계산된 지오해시 문자열 값입니다. 지오해시 문자열은 1~18자일 수 있습니다.

**반환**

[GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 지리 공간 좌표 값입니다. geohash가 유효하지 않은 경우 쿼리는 null 결과를 생성합니다.

> [!NOTE]
> GeoJSON 형식은 경도 1차 및 위도 2를 지정합니다.

**예**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|좌표|longitude|latitude|
|---|---|---|---|
|{<br>  "유형": "포인트",<br>  "코디네이터": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

다음 예제에서는 잘못된 geohash 입력으로 인해 null 결과를 반환합니다.

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|지오 해시 ()(주)(주)|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>예: 빙 맵에 대한 위치 딥 링크 만들기

Geohash 값을 사용하여 지오해시 중심점을 가리켜 Bing 지도에 대한 딥 링크 URL을 만들 수 있습니다.

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

|지오 해시 ()(주)(주)|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|