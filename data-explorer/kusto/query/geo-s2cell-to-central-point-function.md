---
title: geo_s2cell_to_central_point() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_s2cell_to_central_point()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514436"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

S2 셀의 중심을 나타내는 지리 공간 좌표를 계산합니다.

S2 셀에 대한 자세한 내용은 [여기를](http://s2geometry.io/devguide/s2cell_hierarchy)클릭하십시오.

**구문**

`geo_s2cell_to_central_point(`*s2셀*`)`

**인수**

*s2cell*: s2 셀 토큰 문자열 값은 [geo_point_to_s2cell()](geo-point-to-s2cell-function.md)에 의해 계산되었습니다. S2 Cell 토큰 최대 문자열 길이는 16자입니다.

**반환**

[GeoJSON 형식](https://tools.ietf.org/html/rfc7946) 및 [동적](./scalar-data-types/dynamic.md) 데이터 형식의 지리 공간 좌표 값입니다. S2 셀 토큰이 유효하지 않은 경우 쿼리는 null 결과를 생성합니다.

> [!NOTE]
> GeoJSON 형식은 경도 1차 및 위도 2를 지정합니다.

**예**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|좌표|longitude|latitude|
|---|---|---|---|
|{<br>  "유형": "포인트",<br>  "코디네이터": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

다음 예제에서는 잘못된 S2 셀 토큰 입력으로 인해 null 결과를 반환합니다.
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||