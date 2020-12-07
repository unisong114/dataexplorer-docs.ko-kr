---
title: summarize 연산자 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 summarize 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513202"
---
# <a name="summarize-operator"></a>summarize 연산자

입력된 테이블의 내용을 집계하는 테이블을 생성합니다.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

판매 거래 수와 과일 및 판매 월별 총 금액이 포함된 테이블을 반환합니다.
출력 열에는 거래 수, 거래 가치, 과일 및 거래가 기록된 월의 시작 날짜/시간이 표시됩니다.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

[0,10.0], [10.0,20.0] 등의 각 간격에 가격을 가진 항목 수를 표시하는 테이블입니다. 이 예제는 개수에 대한 열 및 가격 범위에 대한 열을 가지고 있습니다. 모든 다른 입력된 열은 무시됩니다.

## <a name="syntax"></a>Syntax

*T* `| summarize` [[*Column* `=`] *Aggregation* [`,` ...]] [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>인수

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *집계:* . `count()` 또는 `avg()`와 같은 [집계 함수](summarizeoperator.md#list-of-aggregation-functions)에 대한 호출이며, 열 이름을 인수로 사용합니다. [집계 함수의 목록](summarizeoperator.md#list-of-aggregation-functions)을 참조하세요.
* *GroupExpression:* 입력 데이터를 참조할 수 있는 스칼라 식입니다.
  출력에는 모든 그룹 식에 대한 고유 값만큼 많은 레코드가 포함됩니다.

> [!NOTE]
> 입력 테이블이 비어 있으면 출력이 *GroupExpression* 을 사용하는지 여부에 따라 달라집니다.
>
> * *GroupExpression* 이 제공되지 않으면 출력은 단일(빈) 행이 됩니다.
> * *GroupExpression* 이 제공되면 출력에 행이 없습니다.

## <a name="returns"></a>반환

입력 행은 `by` 식의 같은 값을 가진 그룹으로 배열됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과는 `by` 열 및 계산된 각 집계에 대해 열을 하나 이상 포함하고 있습니다. (일부 집계 함수는 여러 열을 반환합니다.)

결과에는 `by` 값의 고유 조합이 있는 만큼 많은 행이 포함됩니다(0일 수 있음). 제공된 그룹 키가 없으면 결과에 단일 레코드가 포함됩니다.

숫자 값의 범위를 요약하려면 `bin()`을 사용하여 범위를 불연속 값으로 줄입니다.

> [!NOTE]
> * 집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 열 이름을 사용하거나 `bin()`을(를) 숫자 열에 적용하는 것이 더 효율적입니다.
> * 날짜/시간 열에 대한 자동 시간당 bin은 더 이상 지원되지 않습니다. 대신 명시적 범주화를 사용합니다. 예: `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|기능|설명|
|--------|-----------|
|[any()](any-aggfunction.md)|그룹에 대해 비어 있지 않은 임의의 값을 반환합니다.|
|[anyif()](anyif-aggfunction.md)|그룹에 대해 비어 있지 않은 임의의 값을 반환합니다(조건자 사용).|
|[arg_max()](arg-max-aggfunction.md)|인수가 최대화되면 하나 이상의 식을 반환합니다.|
|[arg_min()](arg-min-aggfunction.md)|인수가 최소화되면 하나 이상의 식을 반환합니다.|
|[avg()](avg-aggfunction.md)|그룹 전체의 평균 값을 반환합니다.|
|[avgif()](avgif-aggfunction.md)|그룹 전체의 평균 값을 반환합니다(조건자 사용).|
|[binary_all_and](binary-all-and-aggfunction.md)|그룹에 대한 이진 `AND`를 사용하여 집계 값을 반환합니다.|
|[binary_all_or](binary-all-or-aggfunction.md)|그룹에 대한 이진 `OR`을 사용하여 집계 값을 반환합니다.|
|[binary_all_xor](binary-all-xor-aggfunction.md)|그룹에 대한 이진 `XOR`을 사용하여 집계 값을 반환합니다.|
|[buildschema()](buildschema-aggfunction.md)|`dynamic` 입력의 모든 값을 허용하는 최소 스키마를 반환합니다.|
|[count()](count-aggfunction.md)|그룹의 수를 반환합니다.|
|[countif()](countif-aggfunction.md)|그룹에 대한 조건자를 사용하여 개수를 반환합니다.|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다.|
|[dcountif()](dcountif-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다(조건자 사용).|
|[make_bag()](make-bag-aggfunction.md)|그룹 내의 동적 값에 대한 속성 모음을 반환합니다.|
|[make_bag_if()](make-bag-if-aggfunction.md)|그룹 내의 동적 값에 대한 속성 모음을 반환합니다(조건자 사용).|
|[make_list()](makelist-aggfunction.md)|그룹 내의 모든 값에 대한 목록을 반환합니다.|
|[make_list_if()](makelistif-aggfunction.md)|그룹 내의 모든 값에 대한 목록을 반환합니다(조건자 사용).|
|[make_list_with_nulls ()](make-list-with-nulls-aggfunction.md)|그룹 내의 모든 값(null 값 포함)에 대한 목록을 반환합니다.|
|[make_set()](makeset-aggfunction.md)|그룹 내의 고유 값 세트를 반환합니다.|
|[make_set_if()](makesetif-aggfunction.md)|그룹 내의 고유 값 세트를 반환합니다(조건자 사용).|
|[max()](max-aggfunction.md)|그룹의 최댓값을 반환합니다.|
|[maxif()](maxif-aggfunction.md)|그룹 전체의 최댓값을 반환합니다(조건자 사용).|
|[min()](min-aggfunction.md)|그룹의 최솟값을 반환합니다.|
|[minif()](minif-aggfunction.md)|그룹 전체의 최솟값을 반환합니다(조건자 사용).|
|[percentiles()](percentiles-aggfunction.md)|그룹에 대한 백분위수 근사치를 반환합니다.|
|[percentiles_array()](percentiles-aggfunction.md)|그룹에 대한 백분위수 근사치 집합을 반환합니다.|
|[percentilesw()](percentiles-aggfunction.md)|그룹에 대한 가중치가 적용된 백분위수 근사치를 반환합니다.|
|[percentilesw_array()](percentiles-aggfunction.md)|그룹에 대한 가중치가 적용된 백분위수 근사치 집합을 반환합니다.|
|[stdev()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환합니다.|
|[stdevif()](stdevif-aggfunction.md)|그룹 전체의 표준 편차를 반환합니다(조건자 사용).|
|[sum()](sum-aggfunction.md)|그룹 내 요소의 합계를 반환합니다.|
|[sumif()](sumif-aggfunction.md)|그룹 내 요소의 합계를 반환합니다(조건자 사용).|
|[variance()](variance-aggfunction.md)|그룹 전체의 분산을 반환합니다.|
|[varianceif()](varianceif-aggfunction.md)|그룹 전체의 분산을 반환합니다(조건자 사용).|

## <a name="aggregates-default-values"></a>집계 기본값

다음 표에는 집계의 기본값이 요약되어 있습니다.

연산자       |기본값                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    빈 동적 배열([])          
 나머지          |   null                           

 엔터티에 대해 null 값이 포함된 이러한 집계를 사용하면 null 값이 무시되고 계산에 참여하지 않습니다(아래 예제 참조).

## <a name="examples"></a>예제

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="과일 및 공급자별 가격 요약":::

## <a name="example-unique-combination"></a>예제: 고유 조합

테이블에 있는 `ActivityType` 및 `CompletionStatus`의 고유 조합을 결정합니다. 집계 함수는 없고 group-by 키만 있습니다. 출력에는 해당 결과에 대한 열만 표시됩니다.

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>예제: 최소 및 최대 타임스탬프

Activities 테이블에서 모든 레코드의 최소 및 최대 타임스탬프를 찾습니다. 출력에 행이 한 개만 있으므로 group-by 절은 없습니다.

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>예제: 고유 개수

각 continent(대륙)에 대한 행을 만들어 작업이 수행되는 도시의 수를 표시합니다. "continent"에 대한 값이 거의 없으므로 'by' 절에는 그룹화 함수가 필요하지 않습니다.

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>예제: 히스토그램

다음 예제에서는 각 작업 유형에 대한 히스토그램을 계산합니다. `Duration`에는 많은 값이 있으므로 `bin`을 사용하여 해당 값을 10분 간격으로 그룹화합니다.

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

**집계 기본값 예제**

하나 이상의 빈 group-by 키가 `summarize` 연산자의 입력에 있으면 결과도 비어 있습니다.

빈 group-by 키가 `summarize` 연산자의 입력에 없으면 결과는 `summarize`에서 사용되는 집계의 기본값입니다.

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

집계 평균은 null이 아닌 모든 항목의 합계를 계산하고 계산에 참여한 항목만 계산합니다(null은 고려하지 않음).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

일반 개수는 null을 계산합니다. 

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
