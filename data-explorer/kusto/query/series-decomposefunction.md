---
title: series_decompose() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_decompose()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663602"
---
# <a name="series_decompose"></a>series_decompose()

계열에 분해 변환을 적용합니다.  

계열(동적 숫자 배열)을 포함하는 식을 입력으로 가져와 계절, 추세 및 잔류 구성 요소로 분해합니다.
 
**구문**

`series_decompose(`*시리즈* `[,` *계절성* `,` *동향* `,` *Test_points* `,` *Seasonality_threshold*`])`

**인수**

* *시리즈*: 숫자 값의 배열인 동적 배열 셀, 일반적으로 [메이크 시리즈](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력
* *계절성*: 계절별 분석을 제어하는 정수(
    * -1: series_periods_detect(기본값)를 사용하여 계절성을 자동으로 감지합니다. [series_periods_detect](series-periods-detectfunction.md)
    * 기간: 수통 수의 예상 기간을 지정하는 양수 정수입니다. 예를 들어 계열이 1시간 저장소에 있는 경우 주간 기간은 168개의 저장소입니다.
    * 0 : 계절성 없음 (이 구성 요소 추출을 건너 뛰기).    
* *추세*: 다음 중 하나를 포함하는 추세 분석을 제어하는 문자열:
    * "평균": 추세 구성요소를 평균(x)으로 정의합니다(기본값)
    * "linefit": 선형 회귀를 사용하여 추세 구성 요소를 추출합니다.
    * "없음": 추세가 없으며 이 구성 요소를 추출하지 않습니다.    
* *Test_points*: 0(기본값) 또는 양수 정수, 학습(회귀) 프로세스에서 제외할 계열 끝의 점 수를 지정합니다. 이 매개 변수는 예측 목적으로 설정되어야 합니다.
* *Seasonality_threshold*: *계절성이* 자동 감지로 설정된 계절성 점수의 임계값이며 `0.6`기본 점수 임계값은 입니다. 자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)을 참조하십시오.

**반환**

 함수는 다음 각 계열을 반환합니다.

* `baseline`: 계열의 예측값(계절별 및 추세 구성 요소의 합계, 아래 참조)
* `seasonal`: 계절 구성 요소의 시리즈 :
    * 기간이 검색되지 않거나 명시적으로 0으로 설정된 경우: 상수 0
    * 감지되거나 양수 정수로 설정된 경우: 동일한 단계에서 계열 점의 중앙값
* `trend`: 트렌드 구성 요소 시리즈
* `residual`: 잔류 구성 요소 의 시리즈 (즉, x - 기준선)
  

**참고 사항**

* 구성 요소 실행 순서:
    1. 계절 시리즈 추출
    2. x에서 빼서 비계절 계열을 생성합니다.
    3. 디시즌 시리즈에서 트렌드 구성 요소 추출
    4. 기준 만들기 = 계절 + 추세
    5. 잔류 = x - 기준선 작성
    
* 계절성 및/또는 추세를 사용하도록 설정해야 하며, 그렇지 않으면 함수가 중복되고 기준 = 0 및 잔류 = x를 반환합니다.

**시리즈 분해에 대해 자세히 알아보기**

이 메서드는 일반적으로 향후 메트릭 값을 예측하거나 비정상적인 메트릭을 감지하기 위해 주기적인 및/또는 추세 동작(예: 서비스 트래픽 및 기타 사용 메트릭)을 나타낼 것으로 예상되는 측정항목의 시간계에 적용됩니다. 이 회귀 프로세스의 암시적 가정은 (a-priori 알려진) 계절 및 추세 동작과는 별개로, 상기 열계가 스토크되고 무작위로 분포된다는 것입니다. 따라서 잔류 부품의 이상값 검색을 기반으로 비정상적인 값을 감지할 수 있는 반면 계절 및 추세 구성 요소(잔류 부분 무시)에서 미래의 메트릭 값을 예측할 수 있습니다. 자세한 내용은 이 위대한 책의 [타임시리즈 분해 장에서](https://www.otexts.org/fpp/6) 찾을 수 있습니다.

**예**

**1. 주간 계절성**

다음 예제에서는 주간 계절성과 추세가 없는 시리즈를 생성한 다음 이상값을 추가합니다. `series_decompose`계절성을 자동으로 감지하고 계절 구성 요소와 거의 동일한 기준선을 생성합니다. 우리가 추가 한 이상치는 잔류 구성 요소에서 명확하게 볼 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="시리즈 분해 1":::

**2. 추세에 따른 주간 계절성**

이 예제에서는 이전 예제의 계열에 추세를 추가합니다. 첫째, 추세 `series_decompose` `avg` 기본값이 평균만을 취하고 추세를 계산하지 않는 기본 매개 변수로 실행되며, 생성된 기준선이 추세를 포함하지 않고 이전 예제와 비교하여 덜 정확하다는 것을 알 수 있으며 잔차의 추세를 관찰할 때 가장 분명합니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="시리즈 분해 2":::

다음으로 동일한 예제를 실행하지만 시리즈의 추세를 예상하기 때문에 추세 `linefit` 매개 변수에 지정합니다. 긍정적인 추세가 감지되고 기준선이 입력 계열에 훨씬 가깝다는 것을 알 수 있습니다. 잔차는 이상값만 눈에 띄는 0에 가깝습니다. 우리는 차트에서 시리즈의 모든 구성 요소를 볼 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="시리즈 분해 3":::
