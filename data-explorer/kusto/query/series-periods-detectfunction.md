---
title: series_periods_detect() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_periods_detect()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0bc78f93270809774504c1eccbc9fa2bbce6c964
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663437"
---
# <a name="series_periods_detect"></a>series_periods_detect()

타임시리즈에 존재하는 가장 중요한 기간을 찾습니다.  

응용 프로그램의 트래픽을 측정하는 메트릭은 주간 및 일등도의 두 가지 중요한 기간이 특징입니다. 이러한 타임 시리즈를 `series_periods_detect()` 감안할 때, 이러한 2 지배적 인 기간을 감지한다.  
이 함수는 시계열의 동적 배열(일반적으로 [make-series](make-seriesoperator.md) 연산자의 결과 출력)을 포함하는 열을 입력하고, 최소 및 최대 기간 크기(예: 1시간 bin의 경우 일일 기간의 크기는 24개)를 정의하는 두 개의 `real` 숫자, 검색할 함수의 총 기간 수를 정의하는 `long` 숫자로 사용합니다. 이 함수는 다음 2개의 열을 출력합니다.
* *기간*: 발견된 기간(빈 크기 단위로)을 포함하는 동적 배열로 점수별로 정렬됩니다.
* *점수*: 0과 1 사이의 값을 포함하는 동적 배열, 각각의 *기간* 배열에서 각각의 위치에 있는 기간의 중요성을 측정합니다.
 
**구문**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식( 일반적으로 [make-series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력)
* *min_period*: `real` 검색할 최소 기간을 지정하는 숫자입니다.
* *max_period*: `real` 검색할 최대 기간을 지정하는 숫자입니다.
* *num_periods*: `long` 최대 필요한 기간 수를 지정하는 숫자입니다. 출력 동적 배열의 길이입니다.

> [!IMPORTANT]
> * 알고리즘은 계열 길이의 최소 4개 및 대부분의 절반을 포함하는 기간을 감지할 수 있습니다. 
>
> * *min_period* 조금 아래에 설정하고 max_period *max_period* 시간시리즈에서 찾을 것으로 예상되는 기간보다 약간 높여야 합니다. 예를 들어 시간별 집계 신호가 있고 일별 > 및 주간 기간(각각 24개 & 168개)을 *min_period*모두 찾는 경우\*min_period =0.8\*24, *max_period*=1.2 168로 설정할 수 있습니다.
>
> * 입력 타임계는 정규적이어야 합니다( 즉, 상수 저장소로 집계됨).이 경우 항상 [메이크 시리즈를](make-seriesoperator.md)사용하여 작성된 경우입니다. 그렇지 않은 경우 출력은 의미가 없습니다.


**예제**

다음 쿼리는 하루에 두 번 집계된 응용 프로그램 트래픽의 한 달의 스냅숏을 포함합니다(예: 저장소 크기는 12시간).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="시리즈 기간":::

이 `series_periods_detect()` 시리즈에서 실행하면 주간 기간(14점 길이)이 생성됩니다.

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 시리즈\_\_기간은\_\_y 기간을 감지  | 시리즈\_\_기간은\_\_y\_기간 점수를 감지 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


차트에서도 볼 수 있는 일일 기간은 샘플링이 너무 거칠기 때문에(12h bin 크기) 2개의 bin의 일일 기간이 알고리즘에 필요한 최소 기간 크기인 4점을 울부짖는다는 점에 유의하십시오.