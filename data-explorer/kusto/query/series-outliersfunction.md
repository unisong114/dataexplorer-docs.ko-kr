---
title: series_outliers() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_outliers()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663453"
---
# <a name="series_outliers"></a>series_outliers()

시리즈의 이상 점수를 점수로 합니다.

동적 숫자 배열을 포함하는 식을 입력으로 가져와 동일한 길이의 동적 숫자 배열을 생성합니다. 배열의 각 값은 [Tukey의 테스트를](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)사용하여 가능한 변칙 점수를 나타냅니다. 1.5보다 크거나 -1.5보다 작은 값은 각각 입력의 같은 요소에서 증가 또는 감소 비정상을 나타냅니다.   

**구문**

`series_outliers(`*x*`, `*종류*`, `*ignore_val*`, `*min_percentile*`, `*max_percentile*`)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀
* *종류*: 이상값 감지 알고리즘. 현재 `"tukey"` 지원 (전통적인 Tukey) 및 `"ctukey"` (사용자 지정 Tukey). 기본값은 `"ctukey"`
* *ignore_val*: 계열에서 누락 된 값을 나타내는 숫자 값, 기본값은 double (null)입니다. null 및 ignore 값의 점수가 `0`로 설정됩니다.
* *min_percentile*: 일반 간 분위수 범위의 캘레이션의 경우 기본값은 `[2.0, 98.0]` 10, 지원되는 사용자 지정 값은 범위(만)입니다.`ctukey` 
* *max_percentile*: 동일, 기본값은 90, `[2.0, 98.0]` 지원되는 사용자 정의 값은 범위 (ctukey 만)입니다. 

다음 표는 다음과 `"tukey"` 같은 `"ctukey"`차이점을 설명합니다.

| 알고리즘 | 기본 변위치 범위 | 사용자 지정 변위치 범위 지원 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25%/75%              | 예                             |
| `"ctukey"`| 10%/90%              | 예                            |


> [!TIP]
> 이 함수를 사용하는 가장 편리한 방법은 [메이크 시리즈](make-seriesoperator.md) 연산자의 결과에 적용하는 것입니다.

**예제**

이상값을 만드는 노이즈가 있는 시간계가 있고 해당 이상값(노이즈)을 평균 값으로 바꾸려는 경우 series_outliers()를 사용하여 이상값을 감지한 다음 대체할 수 있습니다.

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="시리즈 이상값":::
