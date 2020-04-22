---
title: 연산자 요약 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 연산자 요약에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: ed1808f173d0f779c84f9405987d7395de833120
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663213"
---
# <a name="summarize-operator"></a>summarize 연산자

입력된 테이블의 내용을 집계하는 테이블을 생성합니다.

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

각 공급 업체의 각 과일의 수와 평균 가격을 보여주는 테이블입니다. 과일과 공급 업체의 각 별개의 조합에 대한 출력에 행이있다. 출력 열에는 개수, 평균 가격, 과일 및 공급업체가 표시됩니다. 모든 다른 입력된 열은 무시됩니다.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

[0,10.0], [10.0,20.0] 등의 각 간격에 가격을 가진 항목 수를 표시하는 테이블입니다. 이 예제는 개수에 대한 열 및 가격 범위에 대한 열을 가지고 있습니다. 모든 다른 입력된 열은 무시됩니다.

**구문**

*T* `| summarize` [[[열]*Column* `=` *집계* [...]]`,` [`by` [*[열]* `=` *그룹 표현 [...]]* `,`

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *집계:* 열 이름을 인수로 사용하여 `count()` 또는 `avg()`와 같은 집계 [함수에](summarizeoperator.md#list-of-aggregation-functions) 대한 호출입니다. [집계 함수의 목록](summarizeoperator.md#list-of-aggregation-functions)을 참조하세요.
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. 일반적으로 이미 제한된 값 집합을 제공한 열의 이름 또는 숫자나 시간 열을 인수로 하는 `bin()` 입니다. 

> [!NOTE]
> 입력 테이블이 비어 있으면 출력은 *GroupExpression가* 사용되는지 여부에 따라 달라집니다.
>
> * *GroupExpression이* 제공되지 않으면 출력은 단일(빈) 행이 됩니다.
> * *GroupExpression이* 제공되면 출력에는 행이 없습니다.

**반환**

입력 행은 `by` 식의 같은 값을 가진 그룹으로 배열됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과는 `by` 열 및 계산된 각 집계에 대해 열을 하나 이상 포함하고 있습니다. (일부 집계 함수는 여러 열을 반환합니다.)

결과에는 값의 고유한 조합(0일 `by` 수 있음)만큼의 행이 있습니다. 제공된 그룹 키가 없는 경우 결과에 단일 레코드가 있습니다.

숫자 값 의 범위에 대한 요약하려면 `bin()` 범위를 불연속 값으로 줄이는 데 사용합니다.

> [!NOTE]
> * 집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 열 이름을 사용하거나 `bin()`을(를) 숫자 열에 적용하는 것이 더 효율적입니다.
> * datetime 열에 대한 자동 시간별 저장소는 더 이상 지원되지 않습니다. 대신 명시적 비닝을 사용합니다. 예: `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|함수|Description|
|--------|-----------|
|[any()](any-aggfunction.md)|그룹에 대한 빈 값이 아닌 임의의 값을 반환합니다.|
|[anyif()](anyif-aggfunction.md)|그룹에 대한 임의의 비비어 값을 반환합니다(조건자).|
|[arg_max()](arg-max-aggfunction.md)|인수가 최대화될 때 하나 이상의 식을 반환합니다.|
|[arg_min()](arg-min-aggfunction.md)|인수가 최소화될 때 하나 이상의 식을 반환합니다.|
|[avg()](avg-aggfunction.md)|그룹 전체의 평균 값 반환|
|[avgif()](avgif-aggfunction.md)|그룹 전체의 평균 값 반환(조건자).|
|[binary_all_and](binary-all-and-aggfunction.md)|그룹의 이진을 `AND` 사용하여 집계된 값을 반환합니다.|
|[binary_all_or](binary-all-or-aggfunction.md)|그룹의 이진을 `OR` 사용하여 집계된 값을 반환합니다.|
|[binary_all_xor](binary-all-xor-aggfunction.md)|그룹의 이진을 `XOR` 사용하여 집계된 값을 반환합니다.|
|[buildschema()](buildschema-aggfunction.md)|입력의 모든 값을 인정하는 최소 스키마를 반환합니다. `dynamic`|
|[count()](count-aggfunction.md)|그룹의 개수를 반환합니다.|
|[countif()](countif-aggfunction.md)|그룹의 술어를 가진 개수를 반환합니다.|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다.|
|[dcountif()](dcountif-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다(조건자).|
|[make_bag()](make-bag-aggfunction.md)|그룹 내에서 동적 값의 속성 백을 반환합니다.|
|[make_bag_if()](make-bag-if-aggfunction.md)|그룹 내의 동적 값의 속성 백을 반환합니다(조건자).|
|[make_list()](makelist-aggfunction.md)|그룹 내의 모든 값 목록을 반환합니다.|
|[make_list_if()](makelistif-aggfunction.md)|그룹 내의 모든 값 목록을 반환합니다(조건자 포함).|
|[make_list_with_nulls ()](make-list-with-nulls-aggfunction.md)|null 값을 포함하여 그룹 내의 모든 값 목록을 반환합니다.|
|[make_set()](makeset-aggfunction.md)|그룹 내에서 고유 값 집합을 반환합니다.|
|[make_set_if()](makesetif-aggfunction.md)|그룹 내에서 고유 값 집합을 반환합니다(조건자).|
|[max()](max-aggfunction.md)|그룹 전체의 최대값을 반환합니다.|
|[maxif()](maxif-aggfunction.md)|그룹 전체의 최대값 반환(조건자 사용)|
|[min()](min-aggfunction.md)|그룹 전체의 최소 값 반환|
|[minif()](minif-aggfunction.md)|그룹 전체에서 최소 값을 반환합니다(조건자 사용)|
|[percentiles()](percentiles-aggfunction.md)|그룹의 백분위수 근사치를 반환합니다.|
|[percentiles_array()](percentiles-aggfunction.md)|그룹의 백분위수 근사|
|[백분위수()](percentiles-aggfunction.md)|그룹의 가중치가 가중된 백분위수 근사치를 반환합니다.|
|[percentilesw_array()](percentiles-aggfunction.md)|그룹의 가중치 가중 백분위수 근사 반환|
|[stdev()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환합니다.|
|[stdevif()](stdevif-aggfunction.md)|그룹 전체에서 표준 편차를 반환합니다(조건자).|
|[sum()](sum-aggfunction.md)|그룹과 함께 요소의 합계를 반환합니다.|
|[sumif()](sumif-aggfunction.md)|그룹과 함께 요소의 합계를 반환합니다(조건자).|
|[variance()](variance-aggfunction.md)|그룹 전체의 분산을 반환합니다.|
|[varianceif()](varianceif-aggfunction.md)|그룹 전체의 분산을 반환합니다(조건자).|

## <a name="aggregates-default-values"></a>기본값 집계

다음 표에는 집계의 기본값이 요약되어 있습니다.

연산자       |기본값                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    빈 동적 배열([])          
 나머지          |   null                           

 null 값을 포함하는 엔터티에 대한 이러한 집계를 사용하는 경우 null 값이 무시되고 계산에 참여하지 않습니다(아래 예제 참조).

## <a name="examples"></a>예

![대체 텍스트](./Images/aggregations/01.png "01")

**예제**

테이블에 있는 고유한 `ActivityType` 조합과 `CompletionStatus` 조합을 결정합니다. 집계 함수가 없으며 그룹별 키만 있습니다. 출력에는 해당 결과에 대한 열만 표시됩니다.

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

**예제**

활동 테이블의 모든 레코드의 최소 및 최대 타임스탬프를 찾습니다. 출력에 행이 한 개만 있으므로 group-by 절은 없습니다.

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

**예제**

활동이 발생하는 도시의 수를 표시하여 각 대륙에 대한 행을 만듭니다. "대륙"에 대한 값이 적기 때문에 'by' 절에는 그룹화 함수가 필요하지 않습니다.

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


**예제**

다음 예제는 각 활동 유형에 대한 히스토그램을 계산합니다. 값이 `Duration` 많기 때문에 `bin` 해당 값을 10분 간격으로 그룹화하는 데 사용합니다.

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

**집계 기본값의 예**

연산자의 `summarize` 입력에 하나 이상의 빈 그룹별 키가 있는 경우 결과도 비어 있습니다.

연산자의 `summarize` 입력에 빈 그룹별 키가 없는 경우 결과는 `summarize`다음에 사용되는 집계의 기본값입니다.

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

집계 평균은 모든 비널을 합산하고 계산에 참여한 사람들만 계산합니다(null을 고려하지 않음).

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
