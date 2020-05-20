---
title: 시리즈 운영자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5c1d25c0eaa0a3f52c18cf2f1e5e4200775b7d9d
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550574"
---
# <a name="make-series-operator"></a>make-series 연산자

지정 된 축을 따라 지정 된 일련의 집계 값을 만듭니다.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**구문**

*T* `| make-series` [*MakeSeriesParamters*] [*열* `=` ] *집계* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* [ `from` *start*] [ `to` *end*] `step` *step* [ `by` [*Column* `=` ] *groupexpression* [ `,` ...]]

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *DefaultValue:* 값이 없는 대신 사용 되는 기본값입니다. *AxisColumn* 및 *groupexpression*의 특정 값이 있는 행이 없는 경우 결과에서 배열의 해당 요소에 *DefaultValue*가 할당 됩니다. *DefaultValue* 를 생략 하면 0이 가정 됩니다. 
* *집계:* 열 이름을 인수로 사용 하는 또는와 같은 [집계 함수](make-seriesoperator.md#list-of-aggregation-functions) 에 대 한 호출 `count()` `avg()` 입니다. [집계 함수의 목록](make-seriesoperator.md#list-of-aggregation-functions)을 참조하세요. 숫자 결과를 반환 하는 집계 함수만 연산자와 함께 사용할 수 있습니다 `make-series` .
* *AxisColumn:* 계열이 정렬 될 열입니다. 타임 라인으로 간주할 수 있지만 `datetime` 모든 숫자 형식이 허용 됩니다.
* *start*: (선택 사항) 작성할 각 계열에 대 한 *AxisColumn* 의 하위 값입니다. *start*, *end*및 *step* 은 지정 된 범위 내에서 지정 된 *단계*를 사용 하 여 *AxisColumn* 값의 배열을 만드는 데 사용 됩니다. 모든 *집계* 값은이 배열로 각각 정렬 됩니다. 이 *AxisColumn* 배열은 *AxisColumn*과 이름이 같은 출력의 마지막 출력 열 이기도 합니다. *시작* 값을 지정 하지 않으면 각 계열의 데이터가 있는 첫 번째 bin (단계)이 시작 됩니다.
* *end*: (선택 사항) *AxisColumn*의 상한 (포함 안) 값입니다. 시계열의 마지막 인덱스는이 값 보다 작습니다. 즉, *시작* 하 고 *끝*보다 작은 *단계의* 정수 배수를 추가 합니다. *종료* 값을 제공 하지 않으면 각 계열에 대 한 데이터가 있는 마지막 bin (단계)의 상한이 됩니다.
* *단계*: *AxisColumn* 배열의 두 연속 요소 (즉, bin 크기) 간의 차이입니다.
* *Groupexpression:* 고유 값 집합을 제공 하는 열에 대 한 식입니다. 일반적으로 이미 제한된 값 집합을 제공하는 열 이름입니다. 
* *MakeSeriesParameters*: *Name* `=` 동작을 제어 하는 이름 *값* 형식의 0 개 이상의 (공백으로 구분 된) 매개 변수입니다. 지원 되는 매개 변수는 다음과 같습니다. 
  
  |속성           |값                                        |Description                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |시리즈 시리즈 연산자의 입력이 비어 있는 경우 기본 결과를 생성 합니다.|                                

**반환**

입력 행은 `by` 식과 `bin_at(` *AxisColumn* `, ` *step* `, ` *시작* `)` 식의 값이 동일한 그룹으로 정렬 됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과에는 `by` 열 *AxisColumn* 열과 계산 된 각 집계에 대 한 열이 하나 이상 포함 됩니다. 집계는 여러 열 또는 숫자가 아닌 결과를 지원 하지 않습니다.

이 중간 결과에는 `by` 및 `bin_at(` *AxisColumn* `, ` *step* `, ` *start* `)` 값의 고유 조합이 있으므로 많은 행이 있습니다.

마지막으로 식의 값이 동일한 값이 있는 그룹으로 정렬 된 중간 결과의 행 `by` 과 모든 집계 된 값은 배열 (형식의 값)으로 정렬 됩니다 `dynamic` . 각 집계에 대해 이름이 같은 배열을 포함 하는 하나의 열이 있습니다. Range 함수 출력의 마지막 열에는 모든 *AxisColumn* 값이 사용 됩니다. 해당 값은 모든 행에 대해 반복 됩니다. 

기본적으로 누락 된 bin 채우기로 인해 결과 피벗 테이블에는 모든 계열에 대 한 동일한 bin 수 (즉, 집계 값)가 있습니다.  

**참고**

집계와 그룹화 식에 대해 임의의 식을 제공할 수 있지만 간단한 열 이름을 사용 하는 것이 더 효율적입니다.

**대체 구문**

*T* `| make-series` [*column* `=` ] *집계* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* `in` `range(` *start* `,` *stop* `,` *step* `)` [ `by` [*Column* `=` ] *groupexpression* [ `,` ...]]

대체 구문에서 생성 된 계열은 다음과 같은 두 가지 측면에서 주 구문과 다릅니다.
* *Stop* 값은 포함 되어 있습니다.
* 인덱스 축은 bin ()을 사용 하 여 생성 되 고 bin_at ()로 생성 되지 않습니다. 즉, *시작* 이 생성 된 계열에 포함 되지 않을 수 있습니다.

대체 구문이 아니라 기본 시리즈의 기본 구문을 사용 하는 것이 좋습니다.

**배포 및 순서 섞기**

`make-series``summarize`shufflekey 구문을 사용 하 여 [shufflekey 힌트](shufflequery.md) 를 지원 합니다.

## <a name="list-of-aggregation-functions"></a>집계 함수 목록

|기능|설명|
|--------|-----------|
|[any()](any-aggfunction.md)|그룹에 대 한 비어 있지 않은 임의의 값을 반환 합니다.|
|[avg ()](avg-aggfunction.md)|그룹 전체에서 평균 값을 반환 합니다.|
|[count ()](count-aggfunction.md)|그룹의 수를 반환 합니다.|
|[countif()](countif-aggfunction.md)|그룹의 조건자를 포함 하는 개수를 반환 합니다.|
|[dcount()](dcount-aggfunction.md)|그룹 요소의 대략적인 고유 개수를 반환 합니다.|
|[max ()](max-aggfunction.md)|그룹 전체에서 최대값을 반환 합니다.|
|[min ()](min-aggfunction.md)|그룹 전체에서 최 솟 값을 반환 합니다.|
|[stdev ()](stdev-aggfunction.md)|그룹 전체의 표준 편차를 반환 합니다.|
|[sum ()](sum-aggfunction.md)|그룹 내에 있는 요소의 합을 반환 합니다.|
|[variance()](variance-aggfunction.md)|그룹 간의 분산을 반환 합니다.|

## <a name="list-of-series-analysis-functions"></a>계열 분석 함수 목록

|기능|설명|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[유한 임펄스 응답](https://en.wikipedia.org/wiki/Finite_impulse_response) 필터 적용|
|[series_iir()](series-iirfunction.md)|[무한 임펄스 응답](https://en.wikipedia.org/wiki/Infinite_impulse_response) 필터 적용|
|[series_fit_line()](series-fit-linefunction.md)|입력의 가장 근접 한 직선을 찾습니다.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|입력의 가장 근접 한 줄을 찾습니다. 동적 개체를 반환 합니다.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|입력의 가장 근접 한 두 줄을 찾습니다.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|입력의 가장 근접 한 두 줄을 찾습니다. 동적 개체를 반환 합니다.|
|[series_outliers()](series-outliersfunction.md)|계열에서 변칙 점수 점수|
|[series_periods_detect()](series-periods-detectfunction.md)|시계열에 존재 하는 가장 중요 한 기간을 찾습니다.|
|[series_periods_validate()](series-periods-validatefunction.md)|시계열에 지정 된 길이의 정기적인 패턴이 포함 되어 있는지 여부를 확인 합니다.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|공통 통계 (min/max/variance/stdev/average)를 사용 하 여 여러 열을 반환 합니다.|
|[series_stats()](series-statsfunction.md)|공통 통계 (min/max/variance/stdev/average)를 사용 하 여 동적 값을 생성 합니다.|
  
## <a name="list-of-series-interpolation-functions"></a>계열 보간 함수 목록

|기능|설명|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|계열의 누락 값에 대 한 역방향 채우기 보간을 수행 합니다.|
|[series_fill_const()](series-fill-constfunction.md)|계열의 누락 값을 지정 된 상수 값으로 대체 합니다.|
|[series_fill_forward()](series-fill-forwardfunction.md)|계열의 누락 값에 대 한 전방 채우기 보간을 수행 합니다.|
|[series_fill_linear()](series-fill-linearfunction.md)|계열의 누락 값에 대 한 선형 보간을 수행 합니다.|

* 참고: 기본적으로 보간 함수 `null` 는 누락 값으로 가정 합니다. 따라서 `default=` *double* `null` `make-series` 계열에 보간 함수를 사용 하려는 경우에 double ()을 지정 합니다. 

## <a name="example"></a>예제
  
 지정 된 범위의 타임 스탬프를 기준으로 정렬 된 각 공급 업체의 각 과일에 대 한 숫자 및 평균 가격의 배열을 보여 주는 테이블입니다. 과일과 공급자의 각 고유 조합에 대 한 행이 출력에 있습니다. 출력 열에는 개수, 평균 및 전체 타임 라인 (2016-01-01부터 2016-01-10까지)의 과일, 공급자 및 배열이 표시 됩니다. 모든 배열은 해당 타임 스탬프를 기준으로 정렬 되며 모든 간격이 기본값으로 채워집니다 (이 예제에서는 0). 모든 다른 입력된 열은 무시됩니다.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  

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
|[4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5]|["2017-01-01T00:00:00.0000000 Z", "2017-01-02T00:00:00.0000000 Z", "2017-01-03T00:00:00.0000000 Z", "2017-01-04T00:00:00.0000000 Z", "2017-01-05T00:00:00.0000000 Z", "2017-01-06T00:00:00.0000000 Z", "2017-01-07T00:00:00.0000000 Z", "2017-01-08T00:00:00.0000000 Z", "2017-01-09T00:00:00.0000000 Z"]|  


입력 `make-series` 이 비어 있는 경우의 기본 동작은 `make-series` 빈 결과도 생성 합니다.

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


에서를 사용 `kind=nonempty` `make-series` 하면 기본값의 비어 있지 않은 결과가 생성 됩니다.

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
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000 Z",<br>  "2017-01-02T00:00:00.0000000 Z",<br>  "2017-01-03T00:00:00.0000000 Z",<br>  "2017-01-04T00:00:00.0000000 Z",<br>  "2017-01-05T00:00:00.0000000 Z",<br>  "2017-01-06T00:00:00.0000000 Z",<br>  "2017-01-07T00:00:00.0000000 Z",<br>  "2017-01-08T00:00:00.0000000 Z",<br>  "2017-01-09T00:00:00.0000000 Z"<br>]|
