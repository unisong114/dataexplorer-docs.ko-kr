---
title: series_fit_2lines_dynamic ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fit_2lines_dynamic ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e2671b112ff1a73a5f9bbc10d6537a5e8d7b755e
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618771"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

계열에서 두 세그먼트 선형 회귀를 적용 하 여 동적 개체를 반환 합니다.  

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 계열에서 추세 변경을 식별 하 고 수량화 하기 위해 [두 세그먼트 선형 회귀](https://en.wikipedia.org/wiki/Segmented_regression) 를 적용 합니다. 함수는 계열 인덱스를 반복 하 고 각 반복에서 계열을 2 부분으로 분할 하 고, 각 부분에 별도의 줄 ( [series_fit_line ()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md))을 사용 하 고, 전체 r 제곱을 계산 합니다. 최상의 분할은 r 제곱을 최대화 하는 것입니다. 함수는 다음 내용이 포함 된 동적 값의 매개 변수를 반환 합니다.
* `rsquare`: [r 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합할 가능성이 높은 경우이고 0은 데이터 순서가 완전히 잘못되었으며 어떤 선에도 맞지 않음을 의미합니다.
* `split_idx`: 분할 된 지점의 인덱스 2 개 (0부터 시작)입니다.
* `variance`: 입력 데이터의 분산
* `rvariance`: 두 개의 선 세그먼트를 기준으로 하는 입력 데이터 값 간의 차이를 나타내는 잔여 분산입니다.
* `line_fit`: 가장 적합 한 선의 값을 포함 하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 이 길이는 차트에 주로 사용됩니다.
* `right.rsquare`: 분할 오른쪽에 있는 선의 r 제곱입니다. [series_fit_line ()](series-fit-linefunction.md) 또는[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) 를 참조 하세요.
* `right.slope`: 오른쪽 근사 선의 기울기입니다 (y = ax + b의 a).
* `right.interception`: 왼쪽 근사 줄의 가로채기 (y = ax + b의 b)
* `right.variance`: 분할 오른쪽의 입력 데이터 분산입니다.
* `right.rvariance`: 분할 오른쪽에 있는 입력 데이터의 잔여 분산
* `left.rsquare`: 분할 왼쪽에 있는 선의 r 제곱입니다. [series_fit_line ()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) 를 참조 하세요.
* `left.slope`: 왼쪽 근사 선의 기울기입니다 (y = ax + b의 a).
* `left.interception`: 왼쪽 근사 줄의 가로채기 (y = ax + b의 b)
* `left.variance`: 분할 왼쪽의 입력 데이터 분산입니다.
* `left.rvariance`: 분할 왼쪽에 있는 입력 데이터의 잔여 분산

이 연산자는 [series_fit_2lines](series-fit-2linesfunction.md)와 비슷하지만,와는 `series-fit-2lines` 달리 동적 모음을 반환 합니다.

**구문**

`series_fit_2lines_dynamic(`*.x*`)`

**인수**

* *x*: 숫자 값의 동적 배열입니다.  

> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

**예제**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="계열이 2 줄 맞추기":::
