---
title: series_periods_detect ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_periods_detect ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 876966391e67ad2f8f25a900dfc4c92bf0bfd11e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763262"
---
# <a name="series_periods_detect"></a>series_periods_detect()

시계열에 존재 하는 가장 중요 한 기간을 찾습니다.  

응용 프로그램의 트래픽을 측정 하는 메트릭은 주로 주간 및 일일 이라는 두 가지 중요 한 기간으로 규정 됩니다. 함수는 `series_periods_detect()` 시계열에서 이러한 두 개의 주요 기간을 검색 합니다.  
함수는를 입력으로 사용 합니다.
* 시계열의 동적 배열을 포함 하는 열입니다. 일반적으로이 열은 [시리즈 시리즈](make-seriesoperator.md) 연산자의 결과 출력입니다.
* `real`최소 및 최대 기간 크기를 정의 하는 두 개의 숫자 (검색할 bin의 수)입니다. 예를 들어 1 시간 bin의 경우 일별 기간의 크기는 24가 됩니다. 
* `long`검색 하는 함수에 대 한 총 기간 수를 정의 하는 숫자입니다. 

함수는 두 개의 열을 출력 합니다.
* *기간*: 점수를 기준으로 정렬 된 bin 크기 단위로 검색 된 기간을 포함 하는 동적 배열입니다.
* *점수*: 0에서 1 사이의 값을 포함 하는 동적 배열입니다. 각 배열은 *마침표* 배열의 해당 위치에서 마침표의 중요도를 측정 합니다.
 
**구문**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**인수**

* *x*: 숫자 값 배열 (일반적으로 series 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력 [)](make-seriesoperator.md) 인 동적 배열 스칼라 식입니다.
* *min_period*: `real` 검색할 최소 기간을 지정 하는 숫자입니다.
* *max_period*: `real` 검색할 최대 기간을 지정 하는 숫자입니다.
* *num_periods*: `long` 필요한 최대 기간 수를 지정 하는 숫자입니다. 이 숫자는 출력 동적 배열의 길이가 됩니다.

> [!IMPORTANT]
> * 알고리즘은 4 개 이상의 점이 포함 된 기간과 최대 1/2 계열 길이를 검색할 수 있습니다. 
>
> * 아래에 *min_period* 를 설정 하 고, 시계열에서 찾으려는 기간을 약간 *max_period* 합니다. 예를 들어 시간당 집계 된 신호를 포함 하 고 매일 및 매주 (각각 24 및 168 시간)를 검색 하는 경우 *min_period*= 0.8 \* 24, *max_period*= 1.2 \* 168을 설정 하 고이 기간 주위에 20% 여백을 그대로 둘 수 있습니다.
>
> * 입력 시계열은 일반 이어야 합니다. 즉, 상수 bin으로 집계 되며, [시리즈](make-seriesoperator.md)를 사용 하 여 만든 경우 항상 해당 됩니다. 그렇지 않은 경우 출력은 의미가 없습니다.

**예제**

다음 쿼리는 하루에 두 번 집계 된 응용 프로그램 트래픽의 한 달에 대 한 스냅숏을 포함 합니다. Bin 크기는 12 시간입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="계열 기간":::

`series_periods_detect()`이 시리즈에서를 실행 하면 주간 기간의 14 포인트가 반환 됩니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 계열 \_ 기간 \_ 검색 \_ \_ 기간  | 계열 \_ 기간 \_ 에서 \_ y \_ 마침표 \_ 점수를 검색 합니다. |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


> [!NOTE] 
> 샘플링이 너무 거칠게 (12 시간 bin 크기) 하 여 차트에서 볼 수 있는 일일 기간을 찾을 수 없으므로 매일 2 개의 bin 기간이 알고리즘에서 요구 하는 최소 기간 크기인 4 포인트 보다 작습니다.
