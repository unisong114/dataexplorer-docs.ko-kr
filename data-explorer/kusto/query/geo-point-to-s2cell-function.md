---
title: geo_point_to_s2cell ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 geo_point_to_s2cell ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a094644e6f8a96631e5e1bcf1d2d15cf1ba7caad
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347751"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

지리적 위치에 대 한 S2 셀 토큰 문자열 값을 계산 합니다.

[S2 셀 계층](https://s2geometry.io/devguide/s2cell_hierarchy)에 대해 자세히 알아보세요.

## <a name="syntax"></a>Syntax

`geo_point_to_s2cell(`*경도* `, ` *위도* `, ` *수준*`)`

## <a name="arguments"></a>인수

* *경도*: 지리적 위치의 경도 값입니다. *X* 가 실수이 고 *x* 가 [-180, + 180] 범위 내에 있는 경우 경도 *x* 는 유효한 것으로 간주 됩니다. 
* *위도*: 지리적 위치의 위도 값입니다. Y가 실수이 고 [-90, + 90] 범위의 y 인 경우 위도 y는 유효한 것으로 간주 됩니다. 
* *수준*: 요청 된 `int` 셀 수준을 정의 하는 선택적입니다. 지원 되는 값은 [0, 30] 범위에 있습니다. 지정 하지 않으면 기본값이 `11` 사용 됩니다.

## <a name="returns"></a>반환

지정 된 지리적 위치의 S2 셀 토큰 문자열 값입니다. 좌표가 나 수준이 잘못 된 경우 쿼리에서 빈 결과가 생성 됩니다.

> [!NOTE]
>
> * S2 셀은 유용한 지리 공간적 클러스터링 도구 일 수 있습니다.
> * S2 셀에는 최하위 수준 30에서 85011에서 가장 높은 수준 0에서 00.44 cm ² 까지의 영역 범위가 포함 된 31 개의 계층 수준이 있습니다.
> * S2 셀은 수준을 0에서 30으로 높이는 동안 셀 중심을 그대로 유지 합니다.
> * S2 셀은 구면 표면의 셀이 고 가장자리는 geodesics입니다.
> * 경도 x와 위도 y에서 계산 된 S2 셀 토큰 문자열에 대해 [geo_s2cell_to_central_point ()](geo-s2cell-to-central-point-function.md) 함수를 호출 하는 경우 x 및 y를 반드시 반환 하는 것은 아닙니다.
> * 두 지리적 위치가 서로 매우 유사 하지만 S2 셀 토큰은 서로 다를 수 있습니다.

**인스턴스당 셀 근사 면적 검사 수준 값**

모든 수준에 대해 S2 셀의 크기는 유사 하지만 정확히 같지 않습니다. 인접 한 셀 크기는 점점 더 동일 합니다.

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

[이 S2 셀 통계 리소스에서](https://s2geometry.io/resources/s2cell_statistics)테이블 원본을 찾을 수 있습니다.

[Geo_point_to_geohash ()](geo-point-to-geohash-function.md)도 참조 하세요.

## <a name="examples"></a>예제

S2cell에 의해 집계 된 미국 스톰 이벤트입니다.

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="US s2cell":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

다음 예에서는 좌표 그룹을 찾습니다. 그룹의 모든 좌표 쌍은 S2 셀에 있으며 최대 영역은 1632.45 km²입니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

다음 예에서는 잘못 된 수준 입력 때문에 빈 결과를 생성 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

다음 예에서는 잘못 된 수준 입력 때문에 빈 결과를 생성 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |
