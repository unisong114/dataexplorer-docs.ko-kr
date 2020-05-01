---
title: geo_point_to_s2cell ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_point_to_s2cell ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: d1cd2106865419f9407b3ff464d9852eb5ffb630
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618475"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

지리적 위치에 대 한 S2 셀 토큰 문자열 값을 계산 합니다.

S2 셀에 대 한 자세한 내용을 보려면 [여기](http://s2geometry.io/devguide/s2cell_hierarchy)를 클릭 하십시오.

**구문**

`geo_point_to_s2cell(`*경도*`, `*latitude*위도`, `*수준*`)`

**인수**

* *경도*: 지리적 위치의 경도 값입니다. X가 실수이 고 x가 [-180, + 180] 범위에 있는 경우 경도 x는 유효한 것으로 간주 됩니다. 
* *위도*: 지리적 위치의 위도 값입니다. Y가 실수이 고 범위는 [-90, + 90] 인 경우 위도 y는 유효한 것으로 간주 됩니다. 
* *수준*: 요청 된 `int` 셀 수준을 정의 하는 선택적입니다. 지원 되는 값은 [0, 30] 범위에 있습니다. 지정 하지 않으면 기본값이 사용 `11` 됩니다.

**반환**

지정 된 지리적 위치의 S2 셀 토큰 문자열 값입니다. 좌표 또는 수준이 잘못 된 경우 쿼리에서 빈 결과가 생성 됩니다.

> [!NOTE]
>
> * S2Cell는 유용한 지리 공간적 클러스터링 도구 일 수 있습니다.
> * S2Cell에는 85011, 012.19 km ²부터 최고 수준 0에서 00.44 cm ²의 최하위 수준 30에서 영역 범위가 포함 된 31 수준의 계층이 있습니다.
> * S2Cell는 수준이 0에서 30으로 증가 하는 동안 셀 중심 웰을 유지 합니다.
> * S2Cell는 구의 표면에 있는 셀 이며 가장자리는 geodesics입니다.
> * 경도 x와 위도 y에서 계산 된 s2cell 토큰 문자열에 대해 [geo_s2cell_to_central_point ()](geo-s2cell-to-central-point-function.md) 함수를 호출 해도 항상 x 및 y가 반환 되는 것은 아닙니다.
> * 두 지리적 위치가 서로 매우 유사 하지만 S2 셀 토큰은 서로 다를 수 있습니다.

**인스턴스당 셀 근사 면적 검사 수준 값**

모든 수준에서 s2cell 크기는 유사 하지만 정확히 같지 않습니다. 인접 한 셀 크기는 점점 더 동일 합니다.

|Level|최소 무작위 셀 가장자리 길이 (영국)|최대 무작위 셀 가장자리 길이 (US)|
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
|9|14 km|20km|
|10|7km|10 km|
|11|3 km|5km|
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
|22|166 cm|2 m|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30cm|
|26|10 cm|15cm|
|27|5cm|7cm|
|28|2 cm|4cm|
|29|12 mm|18mm|
|30|60mm|9mm|

테이블 원본은 [여기](http://s2geometry.io/resources/s2cell_statistics)에서 찾을 수 있습니다.

[Geo_point_to_geohash ()](geo-point-to-geohash-function.md)도 참조 하세요.

**예제**

S2cell에 의해 집계 된 미국 스톰 이벤트입니다.

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="US s2cell":::

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

| s2cell |
|--------|
| 88d9b  |

다음 예에서는 좌표 그룹을 찾습니다. 그룹의 모든 좌표 쌍은 최대 1632.45 km²의 s2cell에 상주 합니다.
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

| s2cell | count | 위치 |
|--------|-------|-----------|
| 47b1d  | 2     | ["A", "B"] |
| 47ae3  | 1     | ["C"]     |

다음 예에서는 잘못 된 좌표 입력 때문에 빈 결과를 생성 합니다.
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

다음 예에서는 잘못 된 수준 입력 때문에 빈 결과를 생성 합니다.
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

다음 예에서는 잘못 된 수준 입력 때문에 빈 결과를 생성 합니다.
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |