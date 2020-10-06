---
title: series_fit_poly ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_fit_poly ()에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771821"
---
# <a name="series_fit_poly"></a>series_fit_poly ()

독립 변수 (x_series)에서 종속 변수 (y_series)로 다항식 회귀를 적용 합니다. 이 함수는 여러 계열 (동적 숫자 배열)을 포함 하는 테이블을 사용 하 고 [다항식 회귀](https://en.wikipedia.org/wiki/Polynomial_regression)를 사용 하 여 각 계열에 대해 가장 적합 한 최고 수준의 다항식을 생성 합니다. 

> [!TIP]
> * [시리즈 연산자](make-seriesoperator.md)를 사용 하 여 만든 균일 한 간격 계열의 선형 회귀의 경우 간단한 함수 [series_fit_line ()](series-fit-linefunction.md)를 사용 합니다. [예 2](#example-2)를 참조 하세요.
> * *X_series* 를 제공 하 고 높은 수준으로 회귀를 수행 하는 경우 [0-1] 범위를 표준화 하는 것이 좋습니다. [예제 3](#example-3)을 참조 하세요.
> * *X_series* datetime 형식인 경우 double 및 정규화 된 형식으로 변환 해야 합니다. [예제 3](#example-3)을 참조 하세요.
> * 인라인 Python을 사용한 다항식 회귀의 참조 구현은 [series_fit_poly_fl ()](../functions-library/series-fit-poly-fl.md)를 참조 하세요.


## <a name="syntax"></a>구문

`T | extend  series_fit_poly(`*y_series* `, ` *x_series* `, ` *수준*`)`
  
## <a name="arguments"></a>인수

|인수| Description| 필수/선택| 참고|
|---|---|---|---|
| *y_series* | [종속 변수](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 동적 숫자 배열입니다. | 필수 |
| *x_series* | [독립 변수](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 동적 숫자 배열입니다. | 선택 사항입니다. [균일 하지 않게 간격이 있는 계열](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)에만 필요 합니다. | 지정 하지 않으면 기본값인 [1, 2, ..., length (y_series)]로 설정 됩니다.|
| *방법과* | 크기를 조정 하는 데 필요한 다항식의 순서입니다. 예를 들어 선형 회귀의 경우 1, 2 차 회귀의 경우 2입니다. | 선택 사항 | 기본값은 1 (선형 회귀)입니다.|

## <a name="returns"></a>반환

`series_fit_poly()`함수는 다음 열을 반환 합니다.

* `rsquare`: [r 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. 값은 [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합 한 맞춤입니다. 0은 데이터가 정렬 되지 않고 어떠한 줄에도 맞지 않음을 의미 합니다.
* `coefficients`: 최고 거듭제곱 계수부터 가장 낮은 순서로 정렬 된 최고 맞춤 다항식의 계수를 포함 하는 숫자 배열입니다.
* `variance`: 종속 변수의 가변성 (y_series)입니다.
* `rvariance`: 입력 데이터 값 간의 분산 인 잔여 분산입니다.
* `poly_fit`: 가장 적합 한 다항식의 일련의 값을 포함 하는 숫자 배열입니다. 계열 길이는 종속 변수 (y_series)의 길이와 같습니다. 차트에 사용 되는 값입니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1

X & y 축에 노이즈가 있는 다섯 번째 순서 다항식:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="노이즈가 있는 계열에 맞는 다섯 번째 순서 다항식을 보여 주는 그래프":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="노이즈가 있는 계열에 맞는 다섯 번째 순서 다항식을 보여 주는 그래프" border="false":::

### <a name="example-2"></a>예제 2

`series_fit_poly`수준 = 1과 일치 하는지 확인 합니다 `series_fit_line` .

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="노이즈가 있는 계열에 맞는 다섯 번째 순서 다항식을 보여 주는 그래프":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="노이즈가 있는 계열에 맞는 다섯 번째 순서 다항식을 보여 주는 그래프" border="false":::
    
### <a name="example-3"></a>예제 3

불규칙 (균일 하지 않게 간격) 시계열:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="노이즈가 있는 계열에 맞는 다섯 번째 순서 다항식을 보여 주는 그래프":::
