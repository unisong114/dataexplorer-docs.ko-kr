---
title: 요약 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 요약 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513202"
---
# <a name="summarize-operator"></a>summarize 연산자

입력된 테이블의 내용을 집계하는 테이블을 생성합니다.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

판매 트랜잭션 수와 과일 당 총 금액 및 판매 월 수를 포함 하는 테이블을 반환 합니다.
출력 열에는 트랜잭션 수, 거래 가치가 있는 과일 및 트랜잭션이 기록 된 달의 시작 날짜/시간을 표시 합니다.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

[0,10.0], [10.0,20.0] 등의 각 간격에 가격을 가진 항목 수를 표시하는 테이블입니다. 이 예제는 개수에 대한 열 및 가격 범위에 대한 열을 가지고 있습니다. 모든 다른 입력된 열은 무시됩니다.

## <a name="syntax"></a>구문

*T* `| summarize` [[*column* `=` ] *Aggregation* [ `,` ...]] [ `by` [*column* `=` ] *groupexpression* [ `,` ...]]

## <a name="arguments"></a>인수

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *집계:* 열 이름을 인수로 사용 하는 또는와 같은 [집계 함수](summarizeoperator.md#list-of-aggregation-functions) 에 대 한 호출 `count()` `avg()` 입니다. [집계 함수의 목록](summarizeoperator.md#list-of-aggregation-functions)을 참조하세요.
* *Groupexpression:* 입력 데이터를 참조할 수 있는 스칼라 식입니다.
  출력에는 모든 그룹 식에 대 한 고유 값 만큼의 레코드가 포함 됩니다.

> [!NOTE]
> 입력 테이블이 비어 있는 경우 출력은 *Groupexpression* 을 사용 하는지 여부에 따라 달라 집니다.
>
> * *Groupexpression* 을 지정 하지 않으면 출력은 단일 (비어 있는) 행이 됩니다.
> * *Groupexpression* 을 제공 하면 출력에 행이 없습니다.

## <a name="returns"></a>반환

입력 행은 `by` 식의 같은 값을 가진 그룹으로 배열됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과는 `by` 열 및 계산된 각 집계에 대해 열을 하나 이상 포함하고 있습니다. (일부 집계 함수는 여러 열을 반환합니다.)

결과에는 값의 고유 조합 `by` (0 일 수 있음) 만큼의 행이 포함 됩니다. 제공 된 그룹 키가 없는 경우 결과는 단일 레코드를 포함 합니다.

숫자 값의 범위를 요약 하려면를 사용 `bin()` 하 여 불연속 값으로 범위를 줄입니다.

> [!NOTE]
> * 집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 열 이름을 사용하거나 `bin()`을(를) 숫자 열에 적용하는 것이 더 효율적입니다.
> * Datetime 열에 대 한 자동 매시간 저장소는 더 이상 지원 되지 않습니다. 대신 명시적 범주화를 사용 해야 합니다. 예: `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|기능|설명|
|--------|-----------|
|[any ()](any-aggfunction.md)|그룹에 대 한 비어 있지 않은 임의의 값을 반환 합니다.|
|[anyif()](anyif-aggfunction.md)|그룹에 대 한 비어 있지 않은 임의의 값 (조건자 포함)을 반환 합니다.|
|[arg_max()](arg-max-aggfunction.md)|인수가 최대화 된 경우 하나 이상의 식을 반환 합니다.|
|[arg_min()](arg-min-aggfunction.md)|인수가 최소화 될 때 하나 이상의 식을 반환 합니다.|
|[avg ()](avg-aggfunction.md)|그룹 전체에서 평균 값을 반환 합니다.|
|[avgif()](avgif-aggfunction.md)|그룹 전체에서 평균 값 (조건자 포함)을 반환 합니다.|
|[binary_all_and](binary-all-and-aggfunction.md)|그룹의 이진을 사용 하 여 집계 된 값을 반환 합니다. `AND`|
|[binary_all_or](binary-all-or-aggfunction.md)|그룹의 이진을 사용 하 여 집계 된 값을 반환 합니다. `OR`|
|[binary_all_xor](binary-all-xor-aggfunction.md)|그룹의 이진을 사용 하 여 집계 된 값을 반환 합니다. `XOR`|
|[buildschema()](buildschema-aggfunction.md)|입력의 모든 값을 인정 하는 최소 스키마를 반환 합니다. `dynamic`|
|[count ()](count-aggfunction.md)|그룹의 수를 반환 합니다.|
|[countif()](countif-aggfunction.md)|그룹의 조건자를 포함 하는 개수를 반환 합니다.|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환 합니다.|
|[dcountif()](dcountif-aggfunction.md)|그룹 요소의 대략적인 고유 개수 (조건자 포함)를 반환 합니다.|
|[make_bag()](make-bag-aggfunction.md)|그룹 내 동적 값의 속성 모음을 반환 합니다.|
|[make_bag_if()](make-bag-if-aggfunction.md)|그룹 내의 동적 값에 대 한 속성 모음을 반환 합니다 (조건자 포함).|
|[make_list()](makelist-aggfunction.md)|그룹 내의 모든 값 목록을 반환 합니다.|
|[make_list_if()](makelistif-aggfunction.md)|그룹 내 모든 값의 목록 (조건자 포함)을 반환 합니다.|
|[make_list_with_nulls ()](make-list-with-nulls-aggfunction.md)|Null 값을 포함 하 여 그룹 내 모든 값의 목록을 반환 합니다.|
|[make_set()](makeset-aggfunction.md)|그룹 내의 고유한 값 집합을 반환 합니다.|
|[make_set_if()](makesetif-aggfunction.md)|그룹 내의 고유한 값 집합 (조건자 포함)을 반환 합니다.|
|[max()](max-aggfunction.md)|그룹의 최댓값을 반환합니다.|
|[maxif()](maxif-aggfunction.md)|그룹 전체에서 최대 값을 반환 합니다 (조건자 포함).|
|[min()](min-aggfunction.md)|그룹의 최솟값을 반환합니다.|
|[minif()](minif-aggfunction.md)|그룹 전체에서 최 솟 값을 반환 합니다 (조건자 포함).|
|[percentiles()](percentiles-aggfunction.md)|그룹의 백분위 수 근사치를 반환 합니다.|
|[percentiles_array ()](percentiles-aggfunction.md)|그룹의 백분위 수 근사치를 반환 합니다.|
|[percentilesw()](percentiles-aggfunction.md)|그룹의 가중치가 적용 된 백분위 수 근사치를 반환 합니다.|
|[percentilesw_array ()](percentiles-aggfunction.md)|그룹의 가중치가 적용 된 백분위 수 근사치를 반환 합니다.|
|[stdev ()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환 합니다.|
|[stdevif()](stdevif-aggfunction.md)|그룹 전체의 표준 편차를 반환 합니다 (조건자 포함).|
|[sum ()](sum-aggfunction.md)|그룹 내에 있는 요소의 합을 반환 합니다.|
|[sumif()](sumif-aggfunction.md)|그룹 내에 있는 요소의 합 (조건자 포함)을 반환 합니다.|
|[variance()](variance-aggfunction.md)|그룹 간의 분산을 반환 합니다.|
|[varianceif()](varianceif-aggfunction.md)|그룹 전반의 분산 (조건자 포함)을 반환 합니다.|

## <a name="aggregates-default-values"></a>기본값 집계

다음 표에는 집계의 기본 값이 요약 되어 있습니다.

연산자       |기본값                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    빈 동적 배열 ([])          
 나머지          |   null                           

 Null 값을 포함 하는 엔터티에 대해 이러한 집계를 사용 하는 경우 null 값은 무시 되며 계산에 참여 하지 않습니다 (아래 예제 참조).

## <a name="examples"></a>예

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="과일 및 공급 업체 별로 가격 요약":::

## <a name="example-unique-combination"></a>예: 고유 조합

테이블에 있는 및의 고유한 조합을 결정 `ActivityType` `CompletionStatus` 합니다. 집계 함수는 없으며 그룹별 키만 있습니다. 출력에는 해당 결과에 대 한 열만 표시 됩니다.

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>예: 최소 및 최대 타임 스탬프

활동 테이블에서 모든 레코드의 최소 및 최대 타임 스탬프를 찾습니다. 출력에 행이 한 개만 있으므로 group-by 절은 없습니다.

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>예: 고유 카운트

각 대륙에 대해 작업을 수행 하는 도시 수를 표시 하는 행을 만듭니다. "대륙"의 값이 거의 없으므로 ' by ' 절에는 그룹화 함수가 필요 하지 않습니다.

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>예: 히스토그램

다음 예에서는 각 활동 유형에 대 한 히스토그램을 계산 합니다. 에는 `Duration` 많은 값이 있으므로를 사용 `bin` 하 여 값을 10 분 간격으로 그룹화 합니다.

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**집계 기본값 값의 예**

연산자 입력에 `summarize` 비어 있는 group by 키가 하나 이상 있는 경우 결과는 비어 있습니다.

연산자 입력에 `summarize` 빈 group by key가 없는 경우 결과는에 사용 되는 집계의 기본값입니다 `summarize` .

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

집계 avg는 모두 null이 아닌 모든 합계를 계산 하며 계산에 참여 하는 것만 계산 합니다 (null을 고려 하지 않음).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

일반 카운트는 null을 계산 합니다. 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5.0]|[5.0]|
