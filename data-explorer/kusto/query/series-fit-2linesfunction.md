---
title: series_fit_2lines ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fit_2lines ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 89e93a3c4365e0f215a797ee0b01669e6bcbdcfa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343892"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

계열에서 두 세그먼트 선형 회귀를 적용 하 여 여러 열을 반환 합니다.  

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 계열에서 추세 변화를 식별 하 고 수량화 하기 위해 [두 세그먼트 선형 회귀](https://en.wikipedia.org/wiki/Segmented_regression) 를 적용 합니다. 함수는 계열 인덱스를 반복 합니다. 각 반복에서 함수는 계열을 두 부분으로 분할 하 고, 각 부분에 별도의 줄 ( [series_fit_line ()](series-fit-linefunction.md)사용)을 사용 하 고, 전체 r 제곱을 계산 합니다. 가장 적합한 분할은 r 제곱을 최대화한 분할입니다. 이 함수는 해당 매개 변수를 반환합니다.


|매개 변수  |Description  |
|---------|---------|
|`rsquare`     | [R 제곱](https://en.wikipedia.org/wiki/Coefficient_of_determination) 은 맞춤 품질의 표준 측정값입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합 한 맞춤입니다. 0은 데이터가 정렬 되지 않고 어떠한 줄에도 맞지 않음을 의미 합니다.        |
|`split_idx`     |   두 세그먼트 (0부터 시작)에 대 한 중단점의 인덱스입니다.      |
|`variance`     | 입력 데이터의 분산입니다.        |
|`rvariance`     | 가장 근접 한 두 선 세그먼트를 기준으로 하는 입력 데이터 값 간의 차이를 나타내는 잔여 분산입니다.        |
|`line_fit`     | 가장 적합 한 선의 일련의 값을 포함 하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 주로 차트에 사용 됩니다.        |
|`right_rsquare`     | 분할 오른쪽에 있는 선의 R 제곱입니다. [series_fit_line ()](series-fit-linefunction.md)를 참조 하세요.        |
|`right_slope`     | 오른쪽 근사 선의 기울기입니다 (y = ax + b 형식).         |
|`right_interception`     |  왼쪽 근접 줄의 가로채기 (y = ax + b에서 b)입니다.       |
|`right_variance`    | 분할 우변의 입력 데이터 분산입니다.        |
|`right_rvariance`     | 분할 오른쪽에 있는 입력 데이터의 잔여 분산입니다.        |
|`left_rsquare`     | 분할 왼쪽에 있는 선의 R 제곱입니다. [series_fit_line ()](series-fit-linefunction.md)를 참조 하세요.        |
|`left_slope`    | 왼쪽 근사 선의 기울기입니다 (y = ax + b 형식).        |
|`left_interception`     |   왼쪽 근사 줄 (y = ax + b 폼)의 가로채기      |
|`left_variance`     | 분할 왼쪽에 있는 입력 데이터의 분산입니다.        |
|`left_rvariance`     | 분할 왼쪽에 있는 입력 데이터의 잔여 분산입니다.        |


> [!Note]
> 이 함수는 여러 열을 반환 하므로 다른 함수의 인수로 사용할 수 없습니다.

## <a name="syntax"></a>구문

프로젝트 `series_fit_2lines(` *x*`)`
* 는 앞에서 언급 한 모든 열을 series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx 등의 이름으로 반환 합니다.

project (rs, si, v) = `series_fit_2lines(` *x*`)`
* 는 rs (r-square), si (분할 인덱스), v (분산) 열을 반환 하 고 나머지는 series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit 등으로 표시 됩니다.

extend (rs, si, v) = `series_fit_2lines(` *x*`)`
* rs(r 제곱), si(분할 인덱스) 및 v(분산)만 반환합니다.
  
## <a name="arguments"></a>인수

* *x*: 숫자 값의 동적 배열입니다.  

> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="계열이 2 줄 맞추기":::
