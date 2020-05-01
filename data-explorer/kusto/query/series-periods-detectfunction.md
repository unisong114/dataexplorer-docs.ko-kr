---
title: series_periods_detect ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 series_periods_detect ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 940419fea831f382a62359de28d37bc0b207676b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618635"
---
# <a name="series_periods_detect"></a>series_periods_detect()

시계열에 존재 하는 가장 중요 한 기간을 찾습니다.  

응용 프로그램의 트래픽을 측정 하는 메트릭은 매주 및 매일 이라는 두 가지 중요 한 기간으로 규정 됩니다. 이러한 시계열은 이러한 2 개의 `series_periods_detect()` 주요 기간을 감지 합니다.  
함수는 시계열의 동적 배열을 포함 하는 열을 입력으로 사용 합니다 (일반적으로 결과 [시리즈](make-seriesoperator.md) 연산자의 결과 출력), 최소 `real` 및 최대 기간 크기를 정의 하는 두 개의 숫자 (예: 1 시간 bin의 경우 일일 기간의 크기는 24), 검색 하는 함수에 대 한 총 기간 수를 `long` 정의 하는 숫자입니다. 함수는 두 개의 열을 출력 합니다.
* *기간*: 점수를 기준으로 정렬 된 (bin 크기 단위)가 발견 된 기간을 포함 하는 동적 배열입니다.
* *점수*: 0에서 1 사이의 값을 포함 하는 동적 배열로, 각는 *마침표* 배열의 해당 위치에서 마침표의 중요도를 측정 합니다.
 
**구문**

`series_periods_detect(`*x* `,` *min_period* min_period`,` *max_period* max_period`,` *num_periods*`)`

**인수**

* *x*: 숫자 값 배열 (일반적으로 series 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력 [)](make-seriesoperator.md) 인 동적 배열 스칼라 식입니다.
* *min_period*: 검색할 `real` 최소 기간을 지정 하는 숫자입니다.
* *max_period*: 검색할 `real` 최대 기간을 지정 하는 숫자입니다.
* *num_periods*: 필요한 `long` 최대 기간 수를 지정 하는 숫자입니다. 이는 출력 동적 배열의 길이입니다.

> [!IMPORTANT]
> * 알고리즘은 4 개 이상의 점이 포함 된 기간과 최대 1/2 계열 길이를 검색할 수 있습니다. 
>
> * 아래에 *min_period* 를 설정 하 고 시계열에서 찾을 수 있는 기간을 약간 *max_period* 해야 합니다. 예를 들어 매시간 집계 된 신호가 있고 매일 >와 주간 기간 (각각 24 & 168)을 검색 하는 경우 *min_period*= 0.8\*24, *max_period*= 1.2\*168을 설정 하 여이 기간 주위에 20% 여백을 남겨 둘 수 있습니다.
>
> * 입력 시계열은 정기적 이어야 합니다. 즉, 상수 bin으로 집계 됩니다. 즉, 시리즈를 사용 하 여 만든 경우에는 항상이 [작업을 수행](make-seriesoperator.md)합니다. 그렇지 않은 경우 출력은 의미가 없습니다.


**예제**

다음 쿼리는 하루에 두 번 집계 된 응용 프로그램 트래픽의 한 달에 대 한 스냅숏을 포함 합니다 (즉, bin 크기는 12 시간).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="계열 기간":::

이 `series_periods_detect()` 시리즈에서를 실행 하면 주별 기간 (14 개 요소)이 발생 합니다.

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 계열\_기간\_검색\_\_기간  | 계열\_기간\_에서\_y\_마침표\_점수를 검색 합니다. |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


샘플링은 너무 거칠게 (12 시간 bin 크기) 되므로 차트에서 볼 수 있는 일일 기간을 찾을 수 없으므로 매일 2 개의 bin 기간이 알고리즘에서 요구 하는 최소 기간 크기인 4 포인트인 아래 됩니다.