---
title: series_decompose_anomalies() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_decompose_anomalies()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663620"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

계열 분해를 기반으로 한 변칙 [감지(series_decompose()](series-decomposefunction.md)참조) 

계열(동적 숫자 배열)을 포함하는 식을 입력으로 가져와 점수가 있는 비정상적인 점을 추출합니다.

**구문**

`series_decompose_anomalies (`*시리즈* `[, ` *임계값* `,` *계절성* `,` *추세* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**인수**

* *시리즈*: 숫자 값의 배열인 동적 배열 셀, 일반적으로 [메이크 시리즈](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력
* *임계값*: 이상 임계값, 경증 또는 더 강한 이상 감지를 위한 기본 값 1.5(k 값)
* *계절성*: 계절별 분석을 제어하는 정수(
    * -1: 계절성 자동 [감지(series_periods_detect](series-periods-detectfunction.md)사용) [기본값] 
    * 0: 계절성 없음(예: 이 구성 요소 추출 건너뛰기)
    * 기간: 양수 정수, 예상 기간을 bins 단위 수로 지정합니다. 예를 들어 계열이 1시간 저장소에 있는 경우 주간 기간은 168개의 저장소입니다.
* *동향*: 추세 분석을 제어하는 문자열, 중 하나를 포함    
    * "평균": 추세 구성 요소를 계열의 평균으로 정의 [기본값]
    * "없음": 추세 없음, 이 구성 요소 추출 건너뛰기 
    * "라인핏": 선형 회귀를 사용하여 추세 구성 요소를 추출합니다.
* *Test_points*: 0 [기본값] 또는 양수 정수, 학습(회귀) 프로세스에서 제외할 계열 끝의 점 수를 지정합니다. 이 매개 변수는 예측 목적으로 설정되어야 합니다.
* *AD_method*: 잔류 타임계에서 변칙 검색 방법을 제어하는 [문자열(series_outliers](series-outliersfunction.md)참조)이 포함됩니다.    
    * "ctukey": 사용자 지정 10-90 번째 백분위수 범위와 [Tukey의 울타리 테스트](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) [기본값]
    * "tukey": 표준 25-75 번째 백분위수 범위와 [Tukey의 울타리 테스트](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)
* *Seasonality_threshold*: *계절성* 점수가 자동 감지로 설정된 경우 계절성 `0.6` 점수의 임계값이며 기본 점수 임계값은 기본 점수 임계값입니다(자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)참조)


**반환**

 함수는 다음 각 계열을 반환합니다.

* `ad_flag`: 각각 위/아래/무변표시(+1, -1, 0)가 포함된 삼차 시리즈
* `ad_score`: 이상 점수
* `baseline`: 분해에 따른 계열의 예측값

**알고리즘에 대한 자세한**

이 함수는 다음 단계를 따릅니다.
1. 기준선 및 잔류 계열을 만들기 위해 각 매개변수를 사용하여 [series_decompose()를](series-decomposefunction.md) 호출합니다.
2. 잔류 계열에 선택한 변칙 감지 방법으로 [series_outliers()를](series-outliersfunction.md) 적용하여 ad_score 계열을 계산합니다.
3. ad_score 임계값을 적용하여 ad_flag 계열을 계산하여 각각 위/아래/무변을 표시합니다.
 
**예**

**1. 주간 계절성 이상 징후 감지**

다음 예제에서는 매주 계절성이 있는 시리즈를 생성한 다음 이상값을 추가합니다. `series_decompose_anomalies`계절성을 자동으로 감지하고 반복패턴을 포착하는 기준선을 생성합니다. 추가한 이상치는 ad_score 구성 요소에서 명확하게 발견할 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="시리즈 분해 이상 1":::

**2. 추세에 따른 주간 계절성 이상 징후 감지**

이 예제에서는 이전 예제의 계열에 추세를 추가합니다. 첫째, 추세 `series_decompose_anomalies` `avg` 기본값이 평균만을 취하고 추세를 계산하지 않는 기본 매개 변수로 실행되며, 생성된 기준선이 추세를 포함하지 않고 이전 예제와 비교하여 덜 정확하다는 것을 알 수 있으므로 데이터에 삽입한 이상값 중 일부는 더 높은 분산으로 인해 검색되지 않습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart   
```
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="시리즈 분해 이상 2":::

다음으로 동일한 예제를 실행하지만 시리즈의 추세를 예상하기 때문에 추세 `linefit` 매개 변수에 지정합니다. 기준선이 입력 계열에 훨씬 더 가깝다는 것을 알 수 있습니다. 삽입한 모든 이상값과 일부 거짓 긍정이 검색됩니다(임계값 튜닝에 대한 다음 예제 참조).

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="시리즈 분해 이상 3":::

**3. 변칙 검색 임계값 조정**

이전 예제에서는 몇 가지 시끄러운 점이 이상 징후로 감지되었으며, 이 예제에서는 이상 검색 임계값을 기본값인 1.5에서 2.5로 증가하여 더 강력한 이상만 감지할 수 있습니다. 이제 데이터에 삽입한 이상값만 검색되는 것을 볼 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="시리즈 분해 이상 4":::
