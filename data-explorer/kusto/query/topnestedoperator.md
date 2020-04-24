---
title: 최상위 중첩 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 상위 중첩 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 296c36f4653bec971c71dc210008af7b0d98959a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505936"
---
# <a name="top-nested-operator"></a>top-nested 연산자

각 수준이 이전 수준 값을 기준으로 하는 드릴다운인 계층적 상위 결과를 생성합니다. 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

대시보드 시각화 시나리오또는 다음과 같은 질문에 답해야 하는 경우에 유용합니다. 그들 각각에 대해 K2의 최상위 M 값이 무엇인지 찾으십시오 (다른 집계 사용); ..."

**구문**

*T* `|` `desc``,``asc` |  `=` `by` `with others=` `of` *Expression1* *N1* *Aggregation1* *AggName1* *ConstExpr1*[ N1 ] Expression1 [ ConstExpr1 ] [ AggName1 ] 집계1 [ [ [...] `top-nested`

**인수**

각 상위 중첩 규칙에 대해:
* *N1*: 각 계층 수준에 대해 반환할 최상위 값의 수입니다. 선택 사항(생략된 경우 모든 고유 값이 반환됩니다).
* *Expression1*: 위쪽 값을 선택할 수 있는 표현식입니다. 일반적으로 *T의*열 이름 또는 이러한 열의 일부 비닝 작업(예: `bin()`)입니다. 
* *ConstExpr1*: 지정된 경우 해당 중첩 수준에 대해 맨 위 값에 포함되지 않은 다른 값에 대한 집계된 결과를 포함하는 추가 행이 추가됩니다.
* *집계1*: [sum()](sum-aggfunction.md)및 [count()](count-aggfunction.md), [max()](max-aggfunction.md), [min()](min-aggfunction.md)및 [dcount()](dcountif-aggfunction.md), [avg()](avg-aggfunction.md), [백분위수()](percentiles-aggfunction.md)및 [백분율(%)](percentiles-aggfunction.md)또는 이러한 집계의 알제브릭 조합 중 하나가 될 수 있는 집계 함수에 대한 호출입니다.
* 선택이 실제로 범위의 "맨 아래"에서 시작되는지 아니면 "맨 위"에서 시작되는지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.

**반환**

입력 열을 포함하는 계층 테이블과 각 열에 대해 각 요소에 대해 동일한 수준에 대한 집계 결과를 포함하도록 새 열이 생성됩니다.
열은 입력 열의 순서와 동일한 순서로 정렬되며 새 생성된 열은 집계된 열에 가깝습니다. 각 레코드에는 이전 모든 수준에 모든 이전 상위 중첩 규칙을 적용한 다음 이 출력에 현재 레벨의 규칙을 적용한 후 각 값이 선택되는 계층 구조가 있습니다.
즉, 레벨 i의 상위 n 값은 레벨 i - 1의 각 값에 대해 계산됩니다.
 
**팁**

* *집계* 결과에 대한 이름 바꾸기 열 사용: T | 컴퓨터번호위치별 위치의 최상위 중첩 3개 = 합계(MachineNumber) ... .

* 반환되는 레코드 수가 상당히 클 수 있습니다. 최대 *(N1*+1) \* *(N2* \* +1) ... \* *(Nm*+1) (여기서 m은 레벨의 수이고 *Ni는* 레벨 i의 최상위 카운트입니다).

* 집계는 위에서 언급한 것 중 하나인 집계 함수가 있는 숫자 열을 수신해야 합니다.

* 이 `with others=` 옵션을 사용하여 특정 수준에서 상위 N 값이 아닌 다른 모든 값의 집계된 값을 가져옵니다.

* 일부 수준에 `with others=` 관심이 없는 경우 null 값이 추가됩니다(aggreagated 열 및 수준 키의 경우 아래 예제 참조).


* 다음과 같은 상위 중첩 된 문을 추가하여 선택한 상위 중첩 후보에 대한 추가 열을 반환 할 수 있습니다 (아래 예제 참조).

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**예제**

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|시스템 상태|aggregated_State|원본|aggregated_Source|끝 위치|aggregated_EndLocation|
|---|---|---|---|---|---|
|캔자스|87771.2355000001|사법 기관|18744.823|FT 스콧|264.858|
|캔자스|87771.2355000001|Public|22855.6206|버클린 (버클린)|488.2457|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|샤론 SPGS|388.7404|
|텍사스|123400.5101|Public|13650.9079|애머릴로|246.2598|
|텍사스|123400.5101|사법 기관|37228.5966|페리턴 ()페리턴 ()|289.3178|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|클로드|421.44|


* 다른 예와 함께 :

```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|시스템 상태|aggregated_State|원본|aggregated_Source|끝 위치|aggregated_EndLocation|
|---|---|---|---|---|---|
|캔자스|87771.2355000001|사법 기관|18744.823|FT 스콧|264.858|
|캔자스|87771.2355000001|Public|22855.6206|버클린 (버클린)|488.2457|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|샤론 SPGS|388.7404|
|텍사스|123400.5101|Public|13650.9079|애머릴로|246.2598|
|텍사스|123400.5101|사법 기관|37228.5966|페리턴 ()페리턴 ()|289.3178|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|클로드|421.44|
|캔자스|87771.2355000001|사법 기관|18744.823|기타 모든 엔드 위치|18479.965|
|캔자스|87771.2355000001|Public|22855.6206|기타 모든 엔드 위치|22367.3749|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|기타 모든 엔드 위치|20890.9679|
|텍사스|123400.5101|Public|13650.9079|기타 모든 엔드 위치|13404.6481|
|텍사스|123400.5101|사법 기관|37228.5966|기타 모든 엔드 위치|36939.2788|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|기타 모든 엔드 위치|13576.2724|
|캔자스|87771.2355000001|||기타 모든 엔드 위치|24891.0836|
|텍사스|123400.5101|||기타 모든 엔드 위치|58523.2932000001|
|기타 모든 주|1149279.5923|||기타 모든 엔드 위치|1149279.5923|


다음 쿼리는 위의 예제에서 사용된 첫 번째 수준에 대해 동일한 결과를 보여 주며 있습니다.

```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


상위 중첩 된 결과에 다른 열 (EventType)을 요청 : 

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|시스템 상태|aggregated_State|원본|aggregated_Source|끝 위치|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|샤론 SPGS|388.7404|뇌우를 동반한 바람|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|샤론 SPGS|388.7404|우박|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|샤론 SPGS|388.7404|토네이도|
|캔자스|87771.2355000001|Public|22855.6206|버클린 (버클린)|488.2457|우박|
|캔자스|87771.2355000001|Public|22855.6206|버클린 (버클린)|488.2457|뇌우를 동반한 바람|
|캔자스|87771.2355000001|Public|22855.6206|버클린 (버클린)|488.2457|홍수|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|클로드|421.44|우박|
|텍사스|123400.5101|사법 기관|37228.5966|페리턴 ()페리턴 ()|289.3178|우박|
|텍사스|123400.5101|사법 기관|37228.5966|페리턴 ()페리턴 ()|289.3178|홍수|
|텍사스|123400.5101|사법 기관|37228.5966|페리턴 ()페리턴 ()|289.3178|갑작스러운 홍수|

결과를 마지막 중첩 수준(EndLocation별로 이 예제에서)으로 정렬하고 이 수준(그룹별)의 각 값에 대한 인덱스 정렬 순서를 지정합니다.

```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|시스템 상태|원본|끝 위치|끝 위치합계|인디케이션|
|---|---|---|---|---|
|텍사스|숙련된 관찰자|클로드|421.44|0|
|텍사스|숙련된 관찰자|애머릴로|316.8892|1|
|텍사스|숙련된 관찰자|달하트|252.6186|2|
|텍사스|숙련된 관찰자|페리턴 ()페리턴 ()|216.7826|3|
|텍사스|사법 기관|페리턴 ()페리턴 ()|289.3178|0|
|텍사스|사법 기관|Leakey|267.9825|1|
|텍사스|사법 기관|브래킷빌|264.3483|2|
|텍사스|사법 기관|길머 (것)과 같은|261.9068|3|
|캔자스|숙련된 관찰자|샤론 SPGS|388.7404|0|
|캔자스|숙련된 관찰자|애트우드 (미국)|358.6136|1|
|캔자스|숙련된 관찰자|레노르라 ()레노르 ()(레|317.0718|2|
|캔자스|숙련된 관찰자|스콧 시티|307.84|3|
|캔자스|Public|버클린 (버클린)|488.2457|0|
|캔자스|Public|애 슐 랜드|446.4218|1|
|캔자스|Public|보호|446.11|2|
|캔자스|Public|미드 주립공원|371.1|3|