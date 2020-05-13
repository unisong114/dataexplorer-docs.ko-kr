---
title: 상위 중첩 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 상위 중첩 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87606442dcc5d7c9e7e0fceec379c37169757c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370780"
---
# <a name="top-nested-operator"></a>top-nested 연산자

각 수준이 이전 수준 값을 기준으로 하는 드릴다운인 계층적 상위 결과를 생성합니다. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

이는 대시보드 시각화 시나리오에서 유용 하거나, "K1의 상위 N 개 값 (일부 집계 사용)에 대 한 대답을 찾을 필요가 있는 경우에 유용 합니다. 각 항목에 대해 K2의 상위-M 값 (다른 집계 사용)을 찾습니다. ..."

**구문**

*T* `|` `top-nested` [*N1*] `of` *Expression1* [ `with others=` *ConstExpr1*] `by` [*AggName1* `=` ] *Aggregation1* [ `asc`  |  `desc` ] [ `,` ...]

**인수**

각 상위 중첩 규칙에 대해 다음을 수행 합니다.
* *N1*: 각 계층 구조 수준에 대해 반환할 상위 값의 수입니다. 선택 사항입니다. 생략 하면 모든 고유 값이 반환 됩니다.
* *Expression1*: 상위 값을 선택 하는 기준이 되는 식입니다. 일반적으로 *T*의 열 이름 이거나 이러한 열에 대 한 일부 범주화 작업 (예: `bin()` )입니다. 
* *ConstExpr1*: 지정 된 경우 적용 가능한 중첩 수준에 대해 상위 값에 포함 되지 않은 다른 값에 대 한 집계 된 결과를 포함 하는 추가 행이 추가 됩니다.
* *Aggregation1*: [sum ()](sum-aggfunction.md), [count ()](count-aggfunction.md), [max ()](max-aggfunction.md), [min ()](min-aggfunction.md), [dcount ()](dcountif-aggfunction.md), [avg ()](avg-aggfunction.md), [백분위](percentiles-aggfunction.md)수 (), [percentilew ()](percentiles-aggfunction.md)또는 이러한 집계의 algebric 조합 중 하나일 수 있는 집계 함수에 대 한 호출입니다.
* 선택이 실제로 범위의 "맨 아래"에서 시작되는지 아니면 "맨 위"에서 시작되는지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.

**반환**

입력 열을 포함 하는 계층적 테이블 및 각 요소에 대해 동일한 수준의 집계 결과를 포함 하는 새 열이 생성 됩니다.
열이 입력 열의 순서와 동일 하 게 정렬 되 고 새 생성 된 열은 집계 된 열에 가깝습니다. 각 레코드에는 모든 이전 수준에서 모든 이전 상위 중첩 규칙을 적용 한 후에 각 값이 선택 되 고이 출력에 현재 수준의 규칙이 적용 되는 계층적 구조가 있습니다.
즉, 수준 i에 대 한 상위 n 개 값은 수준 i-1의 각 값에 대해 계산 됩니다.
 
**팁**

* *집계* 결과에 대해에서 열 이름 바꾸기 사용: T | MachinesNumberForLocation = sum (MachinesNumber)에의 한 상위 중첩 3 위치

* 반환 된 레코드 수는 매우 클 수 있습니다. 최대 (*N1*+ 1) \* (*N2*+ 1) \* ... \* (*Nm*+ 1) (여기서 m은 수준 수이 고 *Ni* 은 수준 i의 상위 수)입니다.

* 집계는 위에서 언급 한 중 하나인 집계 함수를 사용 하 여 숫자 열을 받아야 합니다.

* `with others=`특정 수준에서 상위 N 개 값이 아닌 다른 모든 값의 집계 된 값을 가져오려면 옵션을 사용 합니다.

* 특정 수준에 대해 관심이 없는 경우에는 `with others=` null 값이 추가 됩니다 (aggreagated 열 및 수준 키에 대 한 자세한 내용은 아래 예제 참조).


* 다음과 같이 상위 중첩 된 추가 문을 추가 하 여 선택한 상위 중첩 후보에 대 한 추가 열을 반환할 수 있습니다 (아래 예제 참조).

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|캔자스|87771.2355000001|사법 기관|18744.823|FT SCOTT|264.858|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|
|텍사스|123400.5101|공용|13650.9079|AMARILLO|246.2598|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|CLAUDE|421.44|


* 다른 예제를 사용 하는 경우:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|캔자스|87771.2355000001|사법 기관|18744.823|FT SCOTT|264.858|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|
|텍사스|123400.5101|공용|13650.9079|AMARILLO|246.2598|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|CLAUDE|421.44|
|캔자스|87771.2355000001|사법 기관|18744.823|다른 모든 끝 위치|18479.965|
|캔자스|87771.2355000001|공용|22855.6206|다른 모든 끝 위치|22367.3749|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|다른 모든 끝 위치|20890.9679|
|텍사스|123400.5101|공용|13650.9079|다른 모든 끝 위치|13404.6481|
|텍사스|123400.5101|사법 기관|37228.5966|다른 모든 끝 위치|36939.2788|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|다른 모든 끝 위치|13576.2724|
|캔자스|87771.2355000001|||다른 모든 끝 위치|24891.0836|
|텍사스|123400.5101|||다른 모든 끝 위치|58523.2932000001|
|기타 모든 상태|1149279.5923|||다른 모든 끝 위치|1149279.5923|


다음 쿼리는 위의 예제에서 사용 된 첫 번째 수준에 대해 동일한 결과를 보여 줍니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


상위 중첩 결과에 다른 열 (EventType) 요청: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|뇌우를 동반한 바람|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|우박|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|토네이도|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|우박|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|뇌우를 동반한 바람|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|홍수|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|CLAUDE|421.44|우박|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|우박|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|홍수|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|갑작스러운 홍수|

결과를 마지막 중첩 된 수준 (이 예제에서는 EndLocation)으로 정렬 하 고이 수준 (그룹별)의 각 값에 대해 인덱스 정렬 순서를 지정 하려면 다음을 수행 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|원본|EndLocations|endLocationSums|인덱스|
|---|---|---|---|---|
|텍사스|숙련된 관찰자|CLAUDE|421.44|0|
|텍사스|숙련된 관찰자|AMARILLO|316.8892|1|
|텍사스|숙련된 관찰자|DALHART|252.6186|2|
|텍사스|숙련된 관찰자|PERRYTON|216.7826|3|
|텍사스|사법 기관|PERRYTON|289.3178|0|
|텍사스|사법 기관|LEAKEY|267.9825|1|
|텍사스|사법 기관|BRACKETTVILLE|264.3483|2|
|텍사스|사법 기관|GILMER|261.9068|3|
|캔자스|숙련된 관찰자|SHARON SPGS|388.7404|0|
|캔자스|숙련된 관찰자|ATWOOD|358.6136|1|
|캔자스|숙련된 관찰자|LENORA|317.0718|2|
|캔자스|숙련된 관찰자|SCOTT 도시|307.84|3|
|캔자스|공용|BUCKLIN|488.2457|0|
|캔자스|공용|ASHLAND|446.4218|1|
|캔자스|공용|보호|446.11|2|
|캔자스|공용|MEADE 상태 파킹|371.1|3|
