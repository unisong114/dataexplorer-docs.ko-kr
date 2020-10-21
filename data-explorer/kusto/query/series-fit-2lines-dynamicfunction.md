---
title: series_fit_2lines_dynamic ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fit_2lines_dynamic ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c16e0918ce45df131490686ce20db4086fddf4c0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242124"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

계열에서 두 세그먼트 선형 회귀를 적용 하 여 동적 개체를 반환 합니다.  

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 계열에서 추세 변화를 식별 하 고 수량화 하기 위해 [두 세그먼트 선형 회귀](https://en.wikipedia.org/wiki/Segmented_regression) 를 적용 합니다. 함수는 계열 인덱스를 반복 합니다. 각 반복에서 계열을 두 부분으로 분할 하 고 [series_fit_line ()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)를 사용 하 여 별도의 줄에 맞춥니다. 함수는 두 부분에 대 한 줄을 모두 조정 하 고 총 R 제곱 값을 계산 합니다. R 제곱을 최대화 하는 분할이 가장 좋습니다. 함수는 다음 내용이 포함 된 동적 값의 매개 변수를 반환 합니다.

* `rsquare`: [R 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. [0-1] 범위에 있는 숫자입니다. 여기서 1은 가장 적합 한 맞춤 이며 0은 데이터가 정렬 되지 않고 어떠한 줄에도 맞지 않음을 의미 합니다.
* `split_idx`: 두 세그먼트 (0부터 시작)에 대 한 분리 지점의 인덱스입니다.
* `variance`: 입력 데이터의 분산입니다.
* `rvariance`: 두 개의 선 세그먼트를 기준으로 하는 입력 데이터 값 간의 차이를 나타내는 잔여 분산입니다.
* `line_fit`: 가장 적합 한 선의 값을 포함 하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 차트에 사용 됩니다.
* `right.rsquare`: 분할 오른쪽에 있는 선의 r 제곱입니다. [series_fit_line ()](series-fit-linefunction.md) 또는 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)를 참조 하세요.
* `right.slope`: 오른쪽 근사 선의 기울기입니다 (y = ax + b 형식).
* `right.interception`: 왼쪽 아래에 있는 왼쪽 (y = ax + b에서 b)을 차단 합니다.
* `right.variance`: 분할 오른쪽의 입력 데이터 분산입니다.
* `right.rvariance`: 분할 오른쪽에 있는 입력 데이터의 잔여 분산입니다.
* `left.rsquare`: split 왼쪽에 있는 선의 r 제곱입니다. [series_fit_line ()]을 참조 하십시오. (series-fit-linefunction.md) 또는 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `left.slope`: 왼쪽 근사 선의 기울기입니다 (y = ax + b 형식).
* `left.interception`: 왼쪽 아래 줄 (y = ax + b 폼)의 가로채기
* `left.variance`: 분할 왼쪽의 입력 데이터 분산입니다.
* `left.rvariance`: 분할 왼쪽에 있는 입력 데이터의 잔여 분산입니다.

이 연산자는 [series_fit_2lines](series-fit-2linesfunction.md)와 비슷합니다. 와 달리 `series-fit-2lines` 동적 모음을 반환 합니다.

## <a name="syntax"></a>구문

`series_fit_2lines_dynamic(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 숫자 값의 동적 배열입니다.  

> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="계열이 2 줄 맞추기":::
