---
title: make-series 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 make-series 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 29fdacc9483c21c4a8a148d2134f4082d439bb89
ms.sourcegitcommit: ee49cd8186d4aecd5de1ed6d24db6c7b7a079ac4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549006"
---
# <a name="make-series-operator"></a>make-series 연산자

지정된 축을 따라 지정된 집계 값 계열을 만듭니다.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Syntax

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>인수

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *DefaultValue:* 없는 값 대신 사용할 기본값입니다. 특정 값이 *AxisColumn* 및 *GroupExpression* 인 행이 없으면 결과에서 *DefaultValue* 가 해당 배열 요소에 할당됩니다. *DefaultValue* 를 생략하면 0이 가정됩니다. 
* *집계:* . `count()` 또는 `avg()`와 같은 [집계 함수](make-seriesoperator.md#list-of-aggregation-functions)에 대한 호출이며, 열 이름을 인수로 사용합니다. [집계 함수의 목록](make-seriesoperator.md#list-of-aggregation-functions)을 참조하세요. 숫자 결과를 반환하는 집계 함수만 `make-series` 연산자와 함께 사용할 수 있습니다.
* *AxisColumn:* 계열이 정렬될 열입니다. 타임라인으로 간주할 수 있지만 `datetime` 이외의 모든 숫자 형식이 허용됩니다.
* *start*: (선택 사항) 작성할 각 계열에 대한 *AxisColumn* 의 하한값입니다. *start*, *end* 및 *step* 은 지정된 범위 내에서 지정된 *step* 을 사용하여 *AxisColumn* 값의 배열을 작성하는 데 사용됩니다. 모든 *Aggregation* 값이 이 배열에 각각 정렬됩니다. 이 *AxisColumn* 배열은 *AxisColumn* 과 이름이 같은 출력의 마지막 출력 열이기도 합니다. *start* 값이 지정되지 않으면 각 계열의 데이터가 있는 첫 번째 bin(step)입니다.
* *end*: (선택 사항) *AxisColumn* 의 상한값(비포함)입니다. 시계열의 마지막 인덱스는 이 값보다 작습니다(즉, *start* + *end* 보다 작은 *step* 의 정수 배수임). *end* 값이 제공되지 않으면 각 계열별 데이터가 있는 마지막 bin(step)의 상한값입니다.
* *step*: *AxisColumn* 배열의 두 연속 요소 간의 차이(즉, bin 크기)입니다.
* *GroupExpression:* 고유 값 세트를 제공하는 열에 대한 식입니다. 일반적으로 이미 제한된 값 집합을 제공하는 열 이름입니다. 
* *MakeSeriesParameters*: 동작을 제어하는 *이름* `=` *값* 형식의 0개 이상의 매개 변수(공백으로 구분)입니다. 지원되는 매개 변수는 다음과 같습니다. 
  
  |Name           |값                                        |설명                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |make-series 연산자의 입력이 비어 있으면 기본 결과를 생성합니다.|                                

## <a name="returns"></a>반환

입력 행은 `by` 식 및 `bin_at(`*AxisColumn*`, `*step*`, `*start*`)` 식의 값이 동일한 그룹으로 정렬됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과에는 `by` 열, *AxisColumn* 열 및 계산된 각 집계에 대한 하나 이상의 열이 포함됩니다. (여러 열 또는 숫자가 아닌 결과를 지원하지 않는 집계입니다.)

이 중간 결과에는 `by` 및 `bin_at(`*AxisColumn*`, `*step*`, `*start*`)` 값의 고유한 조합만큼 많은 행이 포함됩니다.

마지막으로 중간 결과의 행이 `by` 식의 동일한 값이 포함되는 그룹으로 정렬되고, 모든 집계 값이 배열(`dynamic` 형식의 값)로 정렬됩니다. 각 집계에 대해 동일한 이름의 배열이 포함되는 하나의 열이 있습니다. range 함수 출력의 마지막 열에는 모든 *AxisColumn* 값이 포함됩니다. 해당 값은 모든 행에 대해 반복됩니다. 

기본값이 누락된 bin 채우기로 인해 피벗 테이블에는 모든 계열에 대해 동일한 수의 bin(즉, 집계 값)이 있습니다.  

**참고**

집계 및 그룹화 식 모두에 대해 임의 식을 제공할 수 있지만, 단순 열 이름을 사용하는 것이 더 효율적입니다.

**대체 구문**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

대체 구문에서 생성된 계열은 다음 두 가지 측면에서 기본 구문과 다릅니다.
* *stop* 값이 포함됩니다.
* 인덱스 축 범주화는 bin_at()이 아닌 bin()을 통해 생성됩니다. 즉, *start* 가 생성된 계열에 포함되지 않을 수 있습니다.

대체 구문이 아니라 make-series의 기본 구문을 사용하는 것이 좋습니다.

**배포 및 순서 섞기**

`make-series`는 hint.shufflekey 구문 힌트를 사용하여 [shufflekey 힌트](shufflequery.md)의 `summarize`를 지원합니다.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|기능|설명|
|--------|-----------|
|[any()](any-aggfunction.md)|그룹에 대해 비어 있지 않은 임의의 값을 반환합니다.|
|[avg()](avg-aggfunction.md)|그룹 전체의 평균 값을 반환합니다.|
|[avgif()](avgif-aggfunction.md)|그룹에 대한 조건자를 사용하여 평균을 반환합니다.|
|[count()](count-aggfunction.md)|그룹의 수를 반환합니다.|
|[countif()](countif-aggfunction.md)|그룹에 대한 조건자를 사용하여 개수를 반환합니다.|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다.|
|[dcountif()](dcountif-aggfunction.md)|그룹에 대한 조건자를 사용하여 대략적인 고유 개수를 반환합니다.|
|[max()](max-aggfunction.md)|그룹의 최댓값을 반환합니다.|
|[maxif()](maxif-aggfunction.md)|그룹에 대한 조건자를 사용하여 최댓값을 반환합니다.|
|[min()](min-aggfunction.md)|그룹의 최솟값을 반환합니다.|
|[minif()](minif-aggfunction.md)|그룹에 대한 조건자를 사용하여 최솟값을 반환합니다.|
|[percentile()](percentiles-aggfunction.md)|그룹의 백분위수 값을 반환합니다.|
|[stdev()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환합니다.|
|[sum()](sum-aggfunction.md)|그룹 내 요소의 합계를 반환합니다.|
|[sumif()](sumif-aggfunction.md)|그룹에 대한 조건자를 사용하여 요소의 합계를 반환합니다.|
|[variance()](variance-aggfunction.md)|그룹 전체의 분산을 반환합니다.|

## <a name="list-of-series-analysis-functions"></a>계열 분석 함수 목록

|기능|설명|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[유한 임펄스 응답](https://en.wikipedia.org/wiki/Finite_impulse_response) 필터를 적용합니다.|
|[series_iir()](series-iirfunction.md)|[무한 임펄스 응답](https://en.wikipedia.org/wiki/Infinite_impulse_response) 필터를 적용합니다.|
|[series_fit_line()](series-fit-linefunction.md)|입력에 가장 근접한 직선을 찾습니다.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|입력에 가장 근접한 직선을 찾아서 동적 개체를 반환합니다.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|입력에 가장 근접한 두 직선을 찾습니다.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|입력에 가장 근접한 두 직선을 찾아서 동적 개체를 반환합니다.|
|[series_outliers()](series-outliersfunction.md)|계열의 변칙 요소에 대한 점수를 계산합니다.|
|[series_periods_detect()](series-periods-detectfunction.md)|시계열에 있는 가장 중요한 기간을 찾습니다.|
|[series_periods_validate()](series-periods-validatefunction.md)|시계열에 지정된 길이의 정기적 패턴이 포함되어 있는지 여부를 확인합니다.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|일반 통계(min/max/variance/stdev/average)를 사용하여 여러 열을 반환합니다.|
|[series_stats()](series-statsfunction.md)|일반 통계(min/max/variance/stdev/average)를 사용하여 동적 값을 생성합니다.|

계열 분석 함수의 전체 목록은 다음을 참조하세요. [계열 처리 함수](scalarfunctions.md#series-processing-functions)

## <a name="list-of-series-interpolation-functions"></a>계열 보간 함수 목록

|기능|설명|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|계열의 누락 값에 대한 역방향 채우기 보간을 수행합니다.|
|[series_fill_const()](series-fill-constfunction.md)|계열의 누락 값을 지정된 상수 값으로 바꿉니다.|
|[series_fill_forward()](series-fill-forwardfunction.md)|계열의 누락 값에 대한 정방향 채우기 보간을 수행합니다.|
|[series_fill_linear()](series-fill-linearfunction.md)|계열의 누락 값에 대한 선형 보간을 수행합니다.|

* 참고: 보간 함수는 기본적으로 `null`을 누락 값으로 간주합니다. 따라서 보간 함수를 계열에 사용하려면 `make-series`에서 `default=`*double*(`null`)을 지정합니다. 

## <a name="example"></a>예제
  
 지정된 범위의 타임스탬프를 기준으로 정렬된 각 공급자의 각 과일에 대한 개수 및 평균 가격의 배열을 보여 주는 테이블입니다. 출력에는 과일 및 공급자의 각 고유 조합에 대한 행이 있습니다. 출력 열에는 과일, 공급자 및 배열(개수, 평균 및 전체 타임라인(2016-01-01부터 2016-01-10까지))이 표시됩니다. 모든 배열이 해당 타임스탬프를 기준으로 정렬되고, 모든 간격이 기본값(다음 예제의 경우 0)으로 채워집니다. 모든 다른 입력된 열은 무시됩니다.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="세 개의 테이블이 있습니다. 첫 번째 테이블에는 원시 데이터가 나열되고, 두 번째 테이블에는 고유한 공급자-과일-날짜 조합만 포함되고, 세 번째 테이블에는 make-series 결과가 포함되어 있습니다.":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|timestamp|
|---|---|
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


`make-series`에 대한 입력이 비어 있으면 `make-series`의 기본 동작도 빈 결과를 생성합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|개수|
|---|
|0|


`make-series`에서 `kind=nonempty`를 사용하면 기본값의 비어 있지 않은 결과가 생성됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|timestamp|
|---|---|
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
