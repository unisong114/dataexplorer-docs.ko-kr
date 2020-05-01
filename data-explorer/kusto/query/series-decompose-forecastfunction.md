---
title: series_decompose_forecast ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 series_decompose_forecast ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f87a7390ab099886e84642b2eb46a8087b6da9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618849"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

계열 분해를 기반으로 예측 합니다.

계열 (동적 숫자 배열)을 포함 하는 식을 입력으로 사용 하 고 마지막 후행 점의 값을 예측 합니다 (분해 방법에 대 한 자세한 내용은 [series_decompose](series-decomposefunction.md) 참조).
 
**구문**

`series_decompose_forecast(`*Series* `,` *Points* `[,` *Seasonality* 계절성`,` *Trend* 추세`,` *Seasonality_threshold* 계열 점수`])`

**인수**

* *Series*: 숫자 값의 배열인 동적 배열 셀입니다. 일반적으로 [series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *Points*: 예측할 계열 끝의 지점 수를 지정 하는 정수입니다 (예측). 이러한 지점은 학습 (재발) 프로세스에서 제외 됩니다.
* *계절성*: 다음 중 하나를 포함 하는 계절 분석을 제어 하는 정수입니다.
    * -1: [series_periods_detect](series-periods-detectfunction.md) 을 사용 하 여 계절성를 자동 검색 합니다 (기본값). 
    * period: 예상 기간을 bin 수로 지정 하는 양의 정수입니다. 예를 들어 계열이 1 시간 bin에 있으면 주간 기간은 168 bin입니다.
    * 0: 계절성 되지 않습니다 (이 구성 요소 추출 건너뛰기).   
* *Trend*: 다음 중 하나를 포함 하는 추세 분석을 제어 하는 문자열입니다.
    * "linefit": 선형 회귀를 사용 하 여 추세 구성 요소를 추출 합니다 (기본값).    
    * "avg": 추세 구성 요소를 평균 (x)으로 정의 합니다.
    * "none": 추세가 없습니다 .이 구성 요소 추출을 건너뜁니다.   
* *Seasonality_threshold*: *계절성* 가 자동 검색으로 설정 된 경우 계절성 점수에 대 한 임계값입니다 `0.6`. 기본 점수 임계값은입니다. 자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)를 참조 하세요.

**돌려**

 예측 계열을 포함 하는 동적 배열
  

**참고 사항**

* 원래 입력 계열의 동적 배열에는 예측할 숫자 *요소* 슬롯이 포함 되어야 합니다. 일반적으로이 작업은 [시리즈 시리즈](make-seriesoperator.md) 를 사용 하 고 예측할 시간대를 포함 하는 종료 시간 범위를 지정 하 여 수행 됩니다.
    
* 계절성 및/또는 추세를 사용 하도록 설정 해야 합니다. 그렇지 않으면 함수가 중복 되 고 0으로 채워진 계열만 반환 합니다.

**예제**

다음 예에서는 주간 계절성 및 작은 상향 추세를 사용 하 여 시간별로 4 주 시리즈를 생성 한 다음 다른 빈 주를 사용 `make-series` 하 고 계열에 추가 합니다. `series_decompose_forecast`는 주 (24 * 7 개 점)를 사용 하 여 호출 되며 계절성 및 추세를 자동으로 검색 하 고 전체 5 주 기간의 예측을 생성 합니다. 

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

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="계열 분해 예측":::
