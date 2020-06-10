---
title: series_fit_line_dynamic ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fit_line_dynamic ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f3fb8361cfb281ad39dee7a15a690c2b94c79bea
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664928"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

계열에 선형 회귀를 적용 하 여 동적 개체를 반환 합니다.  

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 [선형 회귀](https://en.wikipedia.org/wiki/Line_fitting) 를 사용 하 여 가장 적합 한 줄을 찾습니다. 이 함수는 시계열 배열에 사용되며, make-series 연산자의 출력에 적합합니다. 다음 콘텐츠를 사용 하 여 동적 값을 생성 합니다.
* `rsquare`: [r 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합 한 맞춤 이며 0은 데이터가 정렬 되지 않고 아무 줄에도 있지 않음을 의미 합니다.
* `slope`: 근사 선의 기울기입니다 ( *y = ax + b*의 *a*값).
* `variance`: 입력 데이터의 분산
* `rvariance`: 입력 데이터 값과 근사 값 간의 차이를 나타내는 잔여 분산입니다.
* `interception`: 근사 선의 가로채기 ( *y = ax + b*의 *b*-값)
* `line_fit`: 최적 줄의 값을 포함 하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 주로 차트를 작성 하는 데 사용 됩니다.

이 연산자는 [series_fit_line](series-fit-linefunction.md)와 비슷하지만,와는 달리 `series-fit-line` 동적 모음을 반환 합니다.

**구문**

`series_fit_line_dynamic(`*.x*`)`

**인수**

* *x*: 숫자 값의 동적 배열입니다.

> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

**예**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```
 
:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="계열 맞춤 선":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |
 
