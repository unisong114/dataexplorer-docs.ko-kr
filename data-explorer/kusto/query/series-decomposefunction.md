---
title: series_decompose ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_decompose ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9ff0df578f174bc6964e39e799b91068f89a28e4
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793937"
---
# <a name="series_decompose"></a>series_decompose()

계열에 분해 변환을 적용 합니다.  

계열 (동적 숫자 배열)을 포함 하는 식을 입력으로 사용 하 고 계절, 추세 및 잔여 구성 요소로 분해 합니다.
 
## <a name="syntax"></a>구문

`series_decompose(`*계열* `[,` *계절성* `,` *추세* `,` *Test_points* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>인수

* *Series*: 숫자 값 배열인 동적 배열 셀입니다. 일반적으로 [Series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *계절성*: 다음 중 하나를 포함 하는 계절 분석을 제어 하는 정수입니다.
    * -1: [series_periods_detect](series-periods-detectfunction.md) 을 사용 하 여 계절성를 자동으로 검색 합니다 (기본값).
    * period: 예상 기간을 bin 수로 지정 하는 양의 정수입니다. 예를 들어 계열이 1-h bin 이면 주간 기간은 168 bin입니다.
    * 0: 계절성 되지 않습니다 (이 구성 요소 추출 건너뛰기).    
* *추세*: 다음 값 중 하나를 포함 하는 추세 분석을 제어 하는 문자열입니다.
    * "avg": 추세 구성 요소를 평균 (x)으로 정의 합니다 (기본값).
    * "linefit": 선형 회귀를 사용 하 여 추세 구성 요소를 추출 합니다.
    * "none": 추세가 없습니다 .이 구성 요소 추출을 건너뜁니다.    
* *Test_points*: 0 (기본값) 또는 양의 정수 이며, 학습 (재발) 프로세스에서 제외할 계열의 끝에 있는 점의 수를 지정 합니다. 이 매개 변수는 예측을 위해 설정 되어야 합니다.
* *Seasonality_threshold*: *계절성* 가 자동 검색으로 설정 된 경우 계절성 점수에 대 한 임계값입니다. 기본 점수 임계값은 `0.6` 입니다. 자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)를 참조 하세요.

**Return**

 함수는 다음의 각 계열을 반환 합니다.

* `baseline`: 계열의 예측 값 (계절 및 추세 구성 요소의 합계, 아래 참조)
* `seasonal`: 계절 구성 요소의 시리즈입니다.
    * 마침표가 검색 되지 않거나 명시적으로 0: 상수 0으로 설정 된 경우
    * 검색 되거나 양의 정수로 설정 된 경우: 같은 단계에 있는 계열 요소의 중앙값
* `trend`: 추세 구성 요소의 계열입니다.
* `residual`: 나머지 구성 요소 (x-기준선)의 시리즈입니다.
  

**참고**

* 구성 요소 실행 순서:
    1. 계절 시리즈 추출
    2. X에서 빼서 deseasonal 시리즈 생성
    3. Deseasonal 시리즈에서 추세 구성 요소 추출
    4. 기준선 만들기 = 계절 + 추세
    5. 잔여 = x-기준선 만들기
    
* 계절성 and 또는 trend를 사용 하도록 설정 해야 합니다. 그렇지 않으면 함수는 중복 되며 기준선 = 0 및 잔여 = x만 반환 합니다.

**계열 분해에 대 한 자세한 정보**

이 메서드는 일반적으로 주기 및/또는 추세 동작에 필요한 메트릭의 시계열에 적용 됩니다. 메서드를 사용 하 여 미래 메트릭 값을 예측 하거나 비정상 값을 검색할 수 있습니다. 이 회귀 프로세스의 암시적 가정은 계절 및 추세 동작과는 달리 시계열은 추계 되 고 무작위로 분산 된다는 것입니다. 잔여 부분을 무시 하 고 계절 및 추세 구성 요소에서 향후 메트릭 값을 예측 합니다. 잔여 부분만 해당 하는 이상 값 검색을 기준으로 비정상 값을 검색 합니다. 자세한 내용은 [시계열 분해 챕터](https://otexts.com/fpp2/decomposition.html)에서 찾을 수 있습니다.

## <a name="examples"></a>예제

**주간 계절성**

다음 예제에서는 계절성를 사용 하 여 매주 및 추세 없이 계열을 생성 하 고 그에 대 한 이상 값을 추가 합니다. `series_decompose` 계절성를 찾아서 자동으로 검색 하 고 계절 구성 요소와 거의 동일한 기준을 생성 합니다. 추가한 이상 값은 잔차 구성 요소에서 명확 하 게 볼 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/samples/series-decompose1.png" alt-text="계열 분해 1":::

**추세와 함께 주간 계절성**

이 예제에서는 이전 예제의 계열에 추세를 추가 합니다. 먼저 `series_decompose` 기본 매개 변수를 사용 하 여를 실행 합니다. 추세 `avg` 기본값은 평균만 사용 하 고 추세를 계산 하지 않습니다. 생성 된 기준선에 추세가 포함 되지 않습니다. 잔차에서 추세를 관찰 하는 경우이 예제가 이전 예제 보다 정확도가 떨어질 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

다음으로 동일한 예제를 다시 실행 합니다. 계열에서 추세를 예상 하므로 `linefit` trend 매개 변수에서를 지정 합니다. 긍정 추세가 검색 되 고 기준이 입력 계열에 훨씬 더 가까운 것을 볼 수 있습니다. 잔차는 영 (0)에 가깝습니다. 차트에서 계열의 모든 구성 요소를 볼 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/samples/series-decompose3.png" alt-text="계열 분해 3":::
