---
title: geo_point_to_s2cell() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 geo_point_to_s2cell()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 253b850da519aceb0ead9456f7e49d6dd37d78ec
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663750"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

지리적 위치에 대한 S2 셀 토큰 문자열 값을 계산합니다.

S2 셀에 대한 자세한 내용은 [여기를](http://s2geometry.io/devguide/s2cell_hierarchy)클릭하십시오.

**구문**

`geo_point_to_s2cell(`*경도*`, `*위도*`, `*수준*`)`

**인수**

* *경도*: 지리적 위치의 경도 값입니다. 경도 x는 x가 실제 숫자이고 x가 범위 [-180, +180]인 경우 유효한 것으로 간주됩니다. 
* *위도*: 지리적 위치의 위도 값입니다. 위도 y는 y가 실제 숫자이고 y가 범위 [-90, +90]인 경우 유효한 것으로 간주됩니다. 
* *수준*: `int` 요청된 셀 수준을 정의하는 선택적 입니다. 지원되는 값은 [0,30] 범위에 있습니다. 지정되지 않은 경우 `11` 기본값이 사용됩니다.

**반환**

지정된 지리적 위치의 S2 셀 토큰 문자열 값입니다. 좌표 또는 수준이 유효하지 않은 경우 쿼리는 빈 결과를 생성합니다.

> [!NOTE]
>
> * S2Cell은 유용한 지리 공간 클러스터링 도구가 될 수 있습니다.
> * S2Cell은 31단계의 계층 구조를 가지고 있으며, 가장 낮은 레벨인 0에서 00.44cm²까지 85,011,012.19km²에 이르는 영역 커버리지를 30레벨에 도달합니다.
> * S2Cell은 레벨이 0에서 30으로 증가하는 동안 셀 중심을 잘 보존합니다.
> * S2Cell은 구 표면의 셀이며 가장자리는 측지선입니다.
> * 경도 x 및 위도 y에서 계산된 s2cell 토큰 문자열에서 [geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md) 함수를 호출하면 반드시 x와 y를 반환할 필요는 없습니다.
> * 두 지리적 위치가 서로 매우 가깝지만 서로 다른 S2 Cell 토큰이 있을 수 있습니다.

**레벨 값당 S2 셀 근사 영역 커버리지**

모든 수준에서 s2cell의 크기는 비슷하지만 정확히 같지는 않습니다. 가까운 세포 크기는 더 동등한 경향이 있다.

|Level|최소 랜덤 셀 에지 길이(영국)|최대 임의 셀 가장자리 길이(US)|
|--|--|--|
|0|7842 km|7842 km|
|1|3921 km|5004 km|
|2|1825 km|2489 km|
|3|840 km|1310 km|
|4|432 km|636 km|
|5|210 km|315 km|
|6|108 km|156 km|
|7|54 km|78 km|
|8|27 km|39 km|
|9|14 km|20 km|
|10|7 km|10 km|
|11|3 km|5 km|
|12|1699 m|2 km|
|13|850 m|1225 m|
|14|425 m|613 m|
|15|212 m|306 m|
|16|106 m|153 m|
|17|53 m|77 m|
|18|27 m|38 m|
|19|13 m|19 m|
|20|7 m|10 m|
|21|3 m|5 m|
|22|166 cm|2m|
|23|83cm|120cm|
|24|41 cm|60cm|
|25|21cm|30cm|
|26|10cm|15cm|
|27|5cm|7cm|
|28|2cm|4cm|
|29|12 mm|18 mm|
|30|6mm|9mm|

테이블 소스는 [여기에서](http://s2geometry.io/resources/s2cell_statistics)찾을 수 있습니다.

또한 [geo_point_to_geohash()](geo-point-to-geohash-function.md)을 참조하십시오.

**예**

s2cell에 의해 집계 된 미국 폭풍 이벤트.

:::image type="content" source="images/queries/geo/s2cell.png" alt-text="미국 셀":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2셀 |
|--------|
| 88d9b  |

다음 예제는 좌표 그룹을 찾습니다. 그룹의 모든 좌표는 최대 면적 1632.45 km²의 s2cell에 있습니다.
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2셀 | count | 위치 |
|--------|-------|-----------|
| 47b1d  | 2     | ["A","B"] |
| 47ae3  | 1     | ["C"]     |

다음 예제에서는 잘못된 좌표 입력으로 인해 빈 결과가 생성됩니다.
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2셀 |
|--------|
|        |

다음 예제에서는 잘못된 수준 입력으로 인해 빈 결과가 생성됩니다.
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2셀 |
|--------|
|        |

다음 예제에서는 잘못된 수준 입력으로 인해 빈 결과가 생성됩니다.
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2셀 |
|--------|
|        |