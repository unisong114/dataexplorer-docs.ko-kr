---
title: 상위 중첩 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 상위 중첩 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ce56040e2135a455e29a8ff0ce83d832cbf5c5f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243725"
---
# <a name="top-nested-operator"></a>top-nested 연산자

계층 집계 및 상위 값 선택 영역을 생성 합니다. 여기서 각 수준은 이전 수준에 대 한 구체화입니다.

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

`top-nested`연산자는 테이블 형식 데이터를 입력으로, 하나 이상의 집계 절을 허용 합니다.
첫 번째 집계 절 (맨 왼쪽)은 해당 레코드에 대 한 일부 식의 고유한 값에 따라 입력 레코드를 파티션으로 세분 합니다. 그러면 절은 레코드에 대해이 식을 최대화 하거나 최소화 하는 특정 개수의 레코드를 유지 합니다. 그런 다음, 다음 집계 절은 유사한 함수를 중첩 된 방식으로 적용 합니다. 다음 절은 모두 이전 절에서 생성 된 파티션에 적용 됩니다. 이 프로세스는 모든 집계 절에 대해 계속 됩니다.

예를 들어 `top-nested` 다음 질문에 대답 하는 데 연산자를 사용할 수 있습니다. "국가, 영업 사원 및 판매 금액 등 판매 수치를 포함 하는 테이블의 경우 판매액 별 상위 5 개 국가는 무엇 인가요? 이러한 각 국가에서 상위 3 개 직원은 무엇 인가요? "

## <a name="syntax"></a>구문

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*]

여기서 *TopNestedClause* 에는 다음 구문이 있습니다.

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [ `with` `others` `=` *`ConstExpr`* ] `by` [ *`AggName`* `=` ] *`Aggregation`* [ `asc`  |  `desc` ]

## <a name="arguments"></a>인수

각 *TopNestedClause*:

* *`N`*: `long` 이 계층 구조 수준에 대해 반환할 상위 값의 수를 나타내는 형식의 리터럴입니다.
  생략 하면 모든 고유 값이 반환 됩니다.

* *`ExprName`*: 지정 된 경우의 값에 해당 하는 출력 열의 이름을 설정 합니다 *`Expr`* .

* *`Expr`*:이 계층 구조 수준에 대해 반환할 값을 나타내는 입력 레코드에 대 한 식입니다.
  일반적으로이 열은 테이블 형식 입력 (*T*) 또는 이러한 열에 대 한 일부 계산 (예:)에 대 한 열 참조 `bin()` 입니다.

* *`ConstExpr`*: 지정 된 경우 각 계층 구조 수준에 대해 1 개의 레코드가 "위쪽으로 설정 하지 않은" 모든 레코드에 대 한 집계 값과 함께 추가 됩니다.

* *`AggName`*: 지정 하는 경우이 식별자는 *집계*값에 대 한 출력에 열 이름을 설정 합니다.

* *`Aggregation`*: 같은 값을 공유 하는 모든 레코드에 적용할 집계를 나타내는 숫자 식입니다 *`Expr`* . 이 집계의 값은 "top"의 결과 레코드를 결정 합니다.
  
  지원 되는 집계 함수는 다음과 같습니다.
   * [sum ()](sum-aggfunction.md),
   * [count ()](count-aggfunction.md),
   * [max ()](max-aggfunction.md),
   * [min ()](min-aggfunction.md),
   * [dcount ()](dcountif-aggfunction.md),
   * [avg ()](avg-aggfunction.md),
   * [백분위 수 ()](percentiles-aggfunction.md)및
   * [percentilew ()](percentiles-aggfunction.md). 집계의 대 수 조합도 지원 됩니다.

* `asc` 또는 `desc` (기본값)을 선택 하 여 선택 항목이 실제로 집계 된 값 범위의 "아래쪽" 또는 "위쪽" 중 어디에 있는지를 제어할 수 있습니다.

## <a name="returns"></a>반환

이 연산자는 각 집계 절에 대해 두 개의 열이 있는 테이블을 반환 합니다.

* 한 열에는 절 계산의 고유 값 *`Expr`* (지정 된 경우 *ExprName* 열 이름)이 포함 됩니다.

* 하나의 열에 *집계* 계산 결과가 포함 됩니다 (지정 된 경우 *AggregationName* 열 이름 포함).

## <a name="notes"></a>메모

값으로 지정 되지 않은 입력 열은 *`Expr`* 출력 되지 않습니다.
특정 수준에서 모든 값을 가져오려면 다음을 수행 하는 집계 수를 추가 합니다.

* *N* 값을 생략 합니다.
* 열 이름을 값으로 사용 합니다. *`Expr`*
* 는를 `Ignore=max(1)` 집계로 사용 하 고 열을 무시 (또는 프로젝트) `Ignore` 합니다.

집계 절 ((N1 + 1) \* (N2 + 1) ...)의 수를 사용 하 여 레코드 수가 급격 하 게 증가할 수 있습니다. \* *N* 제한이 지정 되지 않은 경우에는 레코드 증가가 훨씬 더 빨라집니다. 이 연산자는 상당한 양의 리소스를 사용할 수 있다는 것을 고려 합니다.

집계 분포가 균일 하지 않은 경우 *N*을 사용 하 여 반환할 고유 값의 수를 제한 하 고 `with others=` *ConstExpr* 옵션을 사용 하 여 다른 모든 사례의 "가중치"에 대 한 표시를 가져옵니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|시스템 상태|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|캔자스|87771.2355000001|사법 기관|18744.823|FT SCOTT|264.858|
|캔자스|87771.2355000001|공용|22855.6206|BUCKLIN|488.2457|
|캔자스|87771.2355000001|숙련된 관찰자|21279.7083|SHARON SPGS|388.7404|
|텍사스|123400.5101|공용|13650.9079|AMARILLO|246.2598|
|텍사스|123400.5101|사법 기관|37228.5966|PERRYTON|289.3178|
|텍사스|123400.5101|숙련된 관찰자|13997.7124|CLAUDE|421.44|

' 기타 사용 ' 옵션을 사용 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|시스템 상태|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|
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


상위 중첩 결과에 다른 열 (EventType)을 요청 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|시스템 상태|aggregated_State|원본|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
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

이 수준 (그룹별)의 각 값에 대해 인덱스 정렬 순서를 지정 하 여 결과를 마지막 중첩 된 수준 (이 예제에서는 EndLocation)으로 정렬 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|시스템 상태|원본|EndLocations|endLocationSums|색인|
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
