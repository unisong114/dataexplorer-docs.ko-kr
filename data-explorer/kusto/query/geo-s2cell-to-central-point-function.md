---
title: geo_s2cell_to_central_point ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_s2cell_to_central_point ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 30075b5a75e273061423a6f1540f44947ef93cec
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347717"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

S2 셀의 중심을 나타내는 지리 공간적 좌표를 계산 합니다.

[S2 셀 계층](https://s2geometry.io/devguide/s2cell_hierarchy)에 대해 자세히 알아보세요.

## <a name="syntax"></a>구문

`geo_s2cell_to_central_point(`*s2cell*`)`

## <a name="arguments"></a>인수

*s2cell*: S2 셀 토큰 문자열 값 [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md)에서 계산 되었습니다. S2 셀 토큰의 최대 문자열 길이는 16 자입니다.

## <a name="returns"></a>반환

[GeoJSON Format](https://tools.ietf.org/html/rfc7946) 및 [dynamic](./scalar-data-types/dynamic.md) data 형식의 지리 공간적 좌표 값입니다. S2 셀 토큰이 잘못 된 경우 쿼리는 null 결과를 생성 합니다.

> [!NOTE]
> GeoJSON 형식은 경도 first와 위도 second를 지정 합니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|좌표|longitude|latitude|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "좌표": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

다음 예에서는 잘못 된 S2 셀 토큰 입력 때문에 null 결과를 반환 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||
