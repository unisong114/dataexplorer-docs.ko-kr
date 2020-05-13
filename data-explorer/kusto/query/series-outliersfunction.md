---
title: series_outliers ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_outliers ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 864638f8e03487a35eefa83fa3951d2ecefc27c7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372549"
---
# <a name="series_outliers"></a>series_outliers()

계열에서 변칙 점수를 점수가 있습니다.

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 길이가 같은 동적 숫자 배열을 생성 합니다. 배열의 각 값은이에 대 한 [테스트](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)를 사용 하 여 변칙의 점수를 나타냅니다. 1.5보다 크거나 -1.5보다 작은 값은 각각 입력의 같은 요소에서 증가 또는 감소 비정상을 나타냅니다.   

**구문**

`series_outliers(`*x* `, ` *종류* `, ` *ignore_val* `, ` *min_percentile* `, ` *max_percentile*`)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀입니다.
* *kind*: 이상 값 검색의 알고리즘입니다. 현재 `"tukey"` (기존에 지 키) 및 `"ctukey"` (사용자 지정 및 키)를 지원 합니다. 기본값은 `"ctukey"`
* *ignore_val*: 계열의 누락 값을 나타내는 숫자 값입니다. 기본값은 double (null)입니다. Null 및 무시 값의 점수는로 설정 됩니다 `0` .
* *min_percentile*: normal 변 위치 범위의 계산의 경우 기본값은 10이 고, 지원 되는 사용자 지정 값은 범위 `[2.0, 98.0]` ( `ctukey` 전용)입니다. 
* *max_percentile*: 동일, 기본값: 90, 지원 되는 사용자 지정 값 범위 `[2.0, 98.0]` (ctukey 전용) 

다음 표에서는와 간의 차이점 `"tukey"` 을 설명 합니다 `"ctukey"` .

| 알고리즘 | 기본 변위치 범위 | 사용자 지정 변위치 범위 지원 |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25%/75%              | 예                             |
| `"ctukey"`| 10%/90%              | 예                            |


> [!TIP]
> 이 함수를 사용 하는 가장 편리한 방법은 [series](make-seriesoperator.md) 연산자의 결과에 적용 하는 것입니다.

**예제**

이상 값을 만드는 일부 노이즈가 있는 시계열이 있고 이러한 이상 값 (노이즈)을 평균값으로 바꾸려는 경우 series_outliers ()를 사용 하 여 이상 값을 검색 한 후 대체할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="계열 이상 값" border="false":::
