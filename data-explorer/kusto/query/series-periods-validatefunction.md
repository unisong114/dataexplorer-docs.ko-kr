---
title: series_periods_validate ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_periods_validate ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: b157c7fef0b9b4d98f08f5e5020803eea3960097
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372494"
---
# <a name="series_periods_validate"></a>series_periods_validate()

시계열에 지정 된 길이의 정기적인 패턴이 포함 되어 있는지 여부를 확인 합니다.  

응용 프로그램의 트래픽을 측정 하는 메트릭은 매주 및/또는 매일로 규정 됩니다. `series_periods_validate()`매주 및 매일 기간에 대 한 검사를 실행 하 여이를 확인할 수 있습니다.

함수는 시계열의 동적 배열 (일반적으로는 [시리즈](make-seriesoperator.md) 연산자의 결과 출력) 및 `real` 유효성 검사를 수행할 기간의 길이를 정의 하는 하나 이상의 숫자를 포함 하는 열을 입력으로 사용 합니다. 

함수는 두 개의 열을 출력 합니다.
* *기간*: 유효성을 검사할 기간 (입력에 제공)을 포함 하는 동적 배열입니다.
* *점수*: *마침표* 배열의 해당 위치에서 마침표의 중요도를 측정 하는 0과 1 사이의 점수를 포함 하는 동적 배열입니다.

**구문**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* `,` . . . ] `)`

**인수**

* *x*: 숫자 값 배열 (일반적으로 series 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력 [)](make-seriesoperator.md) 인 동적 배열 스칼라 식입니다.
* *period1*, *period2*등: 유효성을 `real` 검사할 기간을 지정 하는 숫자를 bin 크기 단위로 지정 합니다. 예를 들어 계열이 1 시간 bin에 있으면 주간 기간은 168 bin입니다.

> [!IMPORTANT]
> * 각 *period* 인수에 대 한 최소값은 **4** 이 고 최대 값은 입력 계열 길이의 1/2입니다. 이러한 범위를 벗어나는 *기간* 인수의 경우 출력 점수는 **0**이 됩니다.
>
> * 입력 시계열은 정기적 이어야 합니다. 즉, 상수 bin으로 집계 됩니다. 즉, 시리즈를 사용 하 여 만든 경우에는 항상이 [작업을 수행](make-seriesoperator.md)합니다. 그렇지 않은 경우 출력은 의미가 없습니다.
> 
> * 함수는 유효성을 검사할 최대 16 개의 기간을 허용 합니다.


**예제**

다음 쿼리는 하루에 두 번 집계 된 응용 프로그램 트래픽의 한 달에 대 한 스냅숏을 포함 합니다 (즉, bin 크기는 12 시간).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="계열 기간":::

`series_periods_validate()`이 시리즈에서를 실행 하 여 주간 기간 (14 개 요소)의 유효성을 검사 하는 경우 5 일간의 유효성을 검사 하는 경우 **0** 점수를 사용 하 여 높은 점수를 발생 시킬 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 계열 \_ 기간 \_ \_ y \_ 마침표의 유효성을 검사 합니다.  | 계열 \_ 기간 \_ \_ y \_ 점수의 유효성 검사 |
|-------------|-------------------|
| [14.0, 10.0] | [0.84, 0.0]  |
