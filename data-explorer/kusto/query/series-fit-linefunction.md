---
title: series_fit_line() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fit_line()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9b36dc51dded4de7e5b57145b918e6741ab8757d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663489"
---
# <a name="series_fit_line"></a>series_fit_line()

계열에 선형 회귀를 적용하여 여러 열을 반환합니다.  

동적 숫자 배열을 포함하는 식을 입력으로 가져와 [선형 회귀를](https://en.wikipedia.org/wiki/Line_fitting) 수행하여 가장 적합한 선을 찾습니다. 이 함수는 시계열 배열에 사용되며, make-series 연산자의 출력에 적합합니다. 다음 열을 생성합니다.
* `rsquare`: [r-square는](https://en.wikipedia.org/wiki/Coefficient_of_determination) 맞춤 품질의 표준 척도입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합할 가능성이 높은 경우이고 0은 데이터 순서가 완전히 잘못되었으며 어떤 선에도 맞지 않음을 의미합니다. 
* `slope`: 근사한 선의 경사(y=ax+b에서의 경사)
* `variance`: 입력 데이터의 분산
* `rvariance`: 입력 데이터 값 사이의 분산인 잔류 분산은 근사한 데이터 값입니다.
* `interception`: 근사한 선의 가로채기(y=ax+b의 b)
* `line_fit`: 최상의 피팅 라인의 일련의 값을 보유한 숫자 배열. 계열 길이는 입력 배열의 길이와 같습니다. 이 길이는 차트에 주로 사용됩니다.

**구문**

`series_fit_line(`*Ⅹ*`)`

**인수**

* *x*: 숫자 값의 동적 배열입니다.

> [!TIP]
> 이 함수를 사용하는 가장 편리한 방법은 [메이크 시리즈](make-seriesoperator.md) 연산자의 결과에 적용하는 것입니다.

**예**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="계열 맞춤 선":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |