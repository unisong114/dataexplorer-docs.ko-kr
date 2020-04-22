---
title: series_decompose_forecast() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_decompose_forecast()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663536"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

계열 분해를 기반으로 예측합니다.

계열(동적 숫자 배열)을 포함하는 식을 입력으로 가져와 마지막 후행 점의 값을 예측합니다(분해 방법에 대한 자세한 내용은 [series_decompose](series-decomposefunction.md) 참조).
 
**구문**

`series_decompose_forecast(`*시리즈* `,` *포인트* `[,` *계절성* `,` *추세* `,` *Seasonality_threshold*`])`

**인수**

* *시리즈*: 숫자 값의 배열인 동적 배열 셀입니다. 일반적으로 [메이크 시리즈](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력.
* *점*: 예측할 계열 끝의 점 수를 정수로 지정합니다(예측). 이러한 점은 학습(회귀) 프로세스에서 제외됩니다.
* *계절성*: 다음 중 하나를 포함하는 계절 분석을 제어하는 정수입니다.
    * -1: series_periods_detect(기본값)를 사용하여 계절성을 자동으로 감지합니다. [series_periods_detect](series-periods-detectfunction.md) 
    * 기간: 양수 정수, 예상 기간을 bin 수로 지정합니다. 예를 들어 계열이 1시간 저장소에 있는 경우 주간 기간은 168개의 저장소입니다.
    * 0 : 계절성 없음 (이 구성 요소 추출을 건너 뛰기).   
* *추세*: 다음 중 하나를 포함하는 추세 분석을 제어하는 문자열:
    * "linefit": 선형 회귀(기본값)를 사용하여 추세 구성요소를 추출합니다.    
    * "평균": 추세 구성요소를 평균(x)으로 정의합니다.
    * "없음": 추세가 없으며 이 구성 요소를 추출하지 않습니다.   
* *Seasonality_threshold*: *계절성이* 자동 감지로 설정된 계절성 점수의 임계값이며 `0.6`기본 점수 임계값은 입니다. 자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)를 참조하십시오.

**반환**

 예측된 계열을 갖춘 동적 배열
  

**참고 사항**

* 원래 입력 계열의 동적 배열에는 예측할 수 있는 숫자 *포인트* 슬롯이 포함되어야 하며, 이는 일반적으로 [메이크 계열을](make-seriesoperator.md) 사용하고 예측할 기간을 포함하는 범위의 종료 시간을 지정하여 수행됩니다.
    
* 계절성 및/또는 추세를 사용하도록 설정해야 하며, 그렇지 않으면 함수가 중복되어 영점으로 채워진 계열을 반환합니다.

**예제**

다음 예제에서는 주간 계절성과 작은 상승 추세가 있는 시간당 곡물에서 4주 씩 `make-series` 일련의 를 생성한 다음 시리즈에 빈 주를 추가하고 추가합니다. `series_decompose_forecast`일주일(24*7포인트)으로 불리며 계절성과 추세를 자동으로 감지하고 전체 5주 기간의 예측을 생성합니다. 

```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="시리즈 분해 예측":::
