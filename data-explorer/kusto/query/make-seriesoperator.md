---
title: 메이크 시리즈 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make-series 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 3fa8f1693b56fc0820b9e0ba6b5f03a9363c9b98
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663718"
---
# <a name="make-series-operator"></a>make-series 연산자

지정된 축을 따라 지정된 집계 값의 계열을 작성합니다. 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**구문**

*T* `| make-series` [*MakeSeriesParamters*] [*[열* `=`]`,` *집계* `default` `=` [ *기본값*] [ [...] `on` *축열* `from` [ *시작]*`to` `step` [`by` *끝*] *단계* `,` [*[열* `=`] 그룹 *식* [...]]

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *기본값:* 부재값 대신 사용할 기본값입니다. *AxisColumn* 및 *GroupExpression의* 특정 값이 있는 행이 없으면 결과에서 배열의 해당 요소가 *DefaultValue*로 할당됩니다. `default =` *DefaultValue가* 생략된 경우 0이 가정됩니다. 
* *집계:* 열 이름을 인수로 사용하여 `count()` 또는 `avg()`와 같은 집계 [함수에](make-seriesoperator.md#list-of-aggregation-functions) 대한 호출입니다. [집계 함수의 목록](make-seriesoperator.md#list-of-aggregation-functions)을 참조하세요. 숫자 결과를 반환하는 집계 함수만 연산자와 `make-series` 함께 사용할 수 있습니다.
* *축열:* 계열이 정렬되는 열입니다. 타임라인으로 간주될 수 있지만 `datetime` 숫자 형식 외에 허용됩니다.
* *시작*: (선택 사항) 각 계열에 대한 *AxisColumn의* 낮은 바운드 값이 빌드됩니다. *시작*- *끝* 및 *단계는* 지정된 범위 내에서 *AxisColumn* 값의 배열을 빌드하고 지정된 *단계를*사용하는 데 사용됩니다. 모든 *집계* 값은 이 배열에 각각 정렬됩니다. 이 *AxisColumn* 배열은 *AxisColumn과*이름이 같은 출력의 마지막 출력 열이기도 합니다. *시작* 값을 지정하지 않으면 시작은 각 계열에 데이터가 있는 첫 번째 bin(단계)입니다.
* *end*: (선택 사항) *AxisColumn의*하이 바운드(비포함) 값은 이 값보다 열렬의 마지막 인덱스가 더작고 *시작되고 끝보다*작은 *단계의* 정수 배수가 *됩니다.* *끝* 값이 제공되지 않으면 각 계열당 데이터가 있는 마지막 bin(단계)의 상한이 됩니다.
* *단계*: *AxisColumn* 배열의 두 연속 요소 간의 차이(즉, 빈 크기)입니다.
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. 일반적으로 이미 제한된 값 집합을 제공하는 열 이름입니다. 
* *MakeSeriesParameters*: 동작을 제어하는 *이름* `=` *값의* 형태로 0 개 이상의 (공간 구분) 매개 변수. 다음 매개 변수가 지원됩니다. 
  
  |이름           |값                                        |설명                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |make-series 연산자의 입력이 비어 있을 때 기본 결과를 생성합니다.|                                

**반환**

입력 행은 `by` 식및 `bin_at(` *AxisColumn*`, `*단계*`, `*시작* `)` 식의 동일한 값을 갖는 그룹으로 정렬됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과에는 열, `by` *AxisColumn* 열 및 계산된 각 집계에 대해 하나 이상의 열이 포함됩니다. (여러 열 또는 숫자가 아닌 결과가 지원되지 않는 집계).

이 중간 결과에는 고유한 `by` 조합과 `bin_at(` *AxisColumn*`, `*단계*`, `*시작* `)` 값만큼 많은 행이 있습니다.

마지막으로 `by` 표현식의 동일한 값을 갖는 그룹으로 정렬된 중간 결과의 행과 모든 집계된 값이 `dynamic` 배열(형식의 값)으로 정렬됩니다. 각 집계에 대해 동일한 이름의 배열을 포함하는 하나의 열이 있습니다. 모든 *AxisColumn* 값이 있는 범위 함수의 출력의 마지막 열입니다. 해당 값은 모든 행에 대해 반복됩니다. 

기본값으로 채우기 누락된 Bin으로 인해 결과 피벗 테이블에는 모든 계열에 대해 동일한 수의 Bin(즉, 집계된 값)이 있습니다.  

**참고**

집계 식과 그룹화 식 모두에 대해 임의의 식을 제공할 수 있지만 간단한 열 이름을 사용하는 것이 더 효율적입니다.

**대체 구문**

*T* `| make-series` [*열* `=`]`default` `=` *집계* [`,` *기본값*] [ [...] `on` *AxisColumn* `in` `,` *stop* `=``,` *start* `)` `by` *step* *Column* *GroupExpression* 시작 중지`,` 단계 [ [ 열 ] 그룹 식 [...]] `range(`

대체 구문에서 생성된 계열은 다음 2가지 측면에서 주 구문과 다릅니다.
* *정지* 값은 포함됩니다.
* 인덱스 축 비닝은 bin_at() 이 아닌 bin()으로 생성되므로 *시작이* 생성된 계열에 포함될 수 없습니다.

대체 구문이 아닌 make-series의 기본 구문을 사용하는 것이 좋습니다.

**배포 및 셔플**

`make-series`구문 `summarize` [힌트.셔플키를 사용하여 셔플키 힌트를](shufflequery.md) 지원합니다.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|함수|Description|
|--------|-----------|
|[any()](any-aggfunction.md)|그룹에 대한 임의의 비빈 값을 반환합니다.|
|[avg()](avg-aggfunction.md)|그룹 전체의 평균 값 Retuns|
|[count()](count-aggfunction.md)|그룹의 반환 개수|
|[countif()](countif-aggfunction.md)|그룹의 술어를 가진 반환 카운트|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환합니다.|
|[max()](max-aggfunction.md)|그룹 전체의 최대값을 반환합니다.|
|[min()](min-aggfunction.md)|그룹 전체의 최소 값 반환|
|[stdev()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환합니다.|
|[sum()](sum-aggfunction.md)|그룹과 함께 요소의 합계를 반환합니다.|
|[variance()](variance-aggfunction.md)|그룹 전체의 분산을 반환합니다.|

## <a name="list-of-series-analysis-functions"></a>계열 분석 기능 목록

|함수|Description|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[유한 임펄스 응답](https://en.wikipedia.org/wiki/Finite_impulse_response) 필터 적용|
|[series_iir()](series-iirfunction.md)|[무한 임펄스 응답](https://en.wikipedia.org/wiki/Infinite_impulse_response) 필터 적용|
|[series_fit_line()](series-fit-linefunction.md)|입력의 가장 좋은 근사치인 직선을 찾습니다.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|동적 개체를 반환하는 입력의 가장 적합한 근사치인 선을 찾습니다.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|입력의 가장 좋은 근사치인 두 줄을 찾습니다.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|동적 개체를 반환하는 입력의 가장 좋은 근사치인 두 줄을 찾습니다.|
|[series_outliers()](series-outliersfunction.md)|시리즈의 이상 점수 점수|
|[series_periods_detect()](series-periods-detectfunction.md)|타임시리즈에 존재하는 가장 중요한 기간을 찾습니다.|
|[series_periods_validate()](series-periods-validatefunction.md)|타임시리즈에 지정된 길이의 주기적 패턴이 포함되어 있는지 확인합니다.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|공통 통계를 가진 여러 열 반환(최소/최대/분산/stdev/평균)|
|[series_stats()](series-statsfunction.md)|공통 통계(최소/최대/분산/stdev/평균)를 통해 동적 값을 생성합니다.|
  
## <a name="list-of-series-interpolation-functions"></a>계열 보간 함수 목록
|함수|Description|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|계열에서 누락된 값의 뒤로 채우기 보간을 수행합니다.|
|[series_fill_const()](series-fill-constfunction.md)|계열의 누락된 값을 지정된 상수 값으로 바꿉꿉|
|[series_fill_forward()](series-fill-forwardfunction.md)|계열에서 누락된 값의 정방향 채우기 보간 수행|
|[series_fill_linear()](series-fill-linearfunction.md)|계열에서 누락된 값의 선형 보간 수행|

* 참고: 보간 함수는 기본적으로 `null` 누락된 값으로 가정합니다. 따라서 계열에 `default=`보간`null`함수를 사용하려는 `make-series` 경우 *double*() 을 지정하는 것이 좋습니다. 

## <a name="example"></a>예제
  
 지정된 범위의 타임스탬프에 의해 정렬된 각 공급업체의 각 과일의 숫자와 평균 가격의 배열을 보여 주는 테이블입니다. 과일과 공급 업체의 각 별개의 조합에 대한 출력에 행이있다. 출력 열은 과일, 공급 업체 및 배열을 보여줍니다 : 카운트, 평균 및 전체 타임 라인 (2016-01-01에서 2016-01-10까지). 모든 배열은 각 타임스탬프별로 정렬되고 모든 간격은 기본값(이 예제에서는 0)으로 채워져 있습니다. 모든 다른 입력된 열은 무시됩니다.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/aggregations/makeseries.png" alt-text="메이크 시리즈":::  
  
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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[2017-01-01T00:00.00000000Z,「2017-01-02T00:00.0000000Z」, "2017-01-03T00:00.000000Z", "2017-01-04T00:00.0000000Z", "2017-01-05T00:00.000000Z", "2017-01-06T00:00.0000000Z", "2017-01-07T00:00.0000000Z", "" 2017-01-08T00:00:00.00000000Z, "2017-01-09T00:00.0000000Z" ]|  


입력이 비어 `make-series` 있으면 기본 동작으로 `make-series` 빈 결과도 생성됩니다.

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


`kind=nonempty` 에서를 `make-series` 사용하면 기본값의 비어 없는 결과가 생성됩니다.

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
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.00000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.00000000Z"<br>]|