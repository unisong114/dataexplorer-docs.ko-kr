---
title: series_fit_2lines_dynamic() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fit_2lines_dynamic()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb2743789c363a33e21ba472a945087b9b277cf
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663529"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

동적 객체를 반환하는 계열에 두 세그먼트 선형 회귀를 적용합니다.  

동적 숫자 배열을 포함하는 식을 입력으로 가져와 계열의 추세 변화를 식별하고 정량화하기 위해 [두 세그먼트 선형 회귀를](https://en.wikipedia.org/wiki/Segmented_regression) 적용합니다. 함수는 계열 인덱스에서 반복되며 각 반복에서 계열을 2부분으로 분할하고 각 부품에 [별도의 선(series_fit_line()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic())을](series-fit-line-dynamicfunction.md)사용하여 총 r-square를 계산합니다. 가장 좋은 분할은 r-square를 최대화한 분할입니다. 함수는 다음과 같은 내용으로 동적 값으로 매개 변수를 반환합니다.
* `rsquare`: [r-square는](https://en.wikipedia.org/wiki/Coefficient_of_determination) 맞춤 품질의 표준 척도입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합할 가능성이 높은 경우이고 0은 데이터 순서가 완전히 잘못되었으며 어떤 선에도 맞지 않음을 의미합니다.
* `split_idx`: 2개 세그먼트에 대한 중단점 인덱스(0기준)
* `variance`: 입력 데이터의 분산
* `rvariance`: 입력 데이터 값 사이의 분산인 잔류 분산은 근사한 데이터(2선 세그먼트별)입니다.
* `line_fit`: 최상의 피팅 라인의 일련의 값을 보유한 숫자 배열. 계열 길이는 입력 배열의 길이와 같습니다. 이 길이는 차트에 주로 사용됩니다.
* `right.rsquare`: 분할 의 오른쪽에있는 라인의 r-사각형, [series_fit_line ()](series-fit-linefunction.md) 또는[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) 참조
* `right.slope`: 오른쪽 근사선의 경사(y=ax+b에서의 경사)
* `right.interception`: 대략적인 왼쪽 선의 가로채기(y=ax+b의 b)
* `right.variance`: 분할 의 오른쪽에 있는 입력 데이터의 분산
* `right.rvariance`: 분할 오른쪽의 입력 데이터의 잔류 분산
* `left.rsquare`: 분할의 왼쪽에 있는 선의 r-사각형, [series_fit_line()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic()](series-fit-line-dynamicfunction.md) 참조
* `left.slope`: 왼쪽 근사선의 경사(y=ax+b에서의 경사)
* `left.interception`: 대략적인 왼쪽 선의 가로채기(y=ax+b의 b)
* `left.variance`: 분할 왼쪽에 있는 입력 데이터의 분산
* `left.rvariance`: 분할 왼쪽에 있는 입력 데이터의 잔류 분산

이 연산자는 [series_fit_2lines](series-fit-2linesfunction.md)비슷하지만 동적 가방을 `series-fit-2lines` 반환합니다.

**구문**

`series_fit_2lines_dynamic(`*Ⅹ*`)`

**인수**

* *x*: 숫자 값의 동적 배열입니다.  

> [!TIP]
> 이 함수를 사용하는 가장 편리한 방법은 [메이크 시리즈](make-seriesoperator.md) 연산자의 결과에 적용하는 것입니다.

**예**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="시리즈 맞춤 2라인":::
