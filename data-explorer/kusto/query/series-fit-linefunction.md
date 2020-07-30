---
title: series_fit_line ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fit_line ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0158753d3d2496e425247202d906633837aa023a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351491"
---
# <a name="series_fit_line"></a>series_fit_line()

계열에 선형 회귀를 적용 하 여 여러 열을 반환 합니다.  

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 [선형 회귀](https://en.wikipedia.org/wiki/Line_fitting) 를 사용 하 여 가장 적합 한 줄을 찾습니다. 이 함수는 시계열 배열에 사용되며, make-series 연산자의 출력에 적합합니다. 함수는 다음 열을 생성 합니다.
* `rsquare`: [r 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. 값은 [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합 한 맞춤입니다. 0은 데이터가 정렬 되지 않고 어떠한 줄에도 맞지 않음을 의미 합니다. 
* `slope`: 근사 선의 기울기입니다 (y = ax + b의 "a").
* `variance`: 입력 데이터의 분산입니다.
* `rvariance`: 입력 데이터 값 간의 분산 인 잔여 분산입니다.
* `interception`: 대략적인 줄 (y = ax + b의 "b")을 가로채기 위한 것입니다.
* `line_fit`: 가장 적합 한 선의 값을 포함 하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 차트에 사용 되는 값입니다.

## <a name="syntax"></a>Syntax

`series_fit_line(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 숫자 값의 동적 배열입니다.

> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="계열 맞춤 선":::

| RSquare | 기울기 | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |
