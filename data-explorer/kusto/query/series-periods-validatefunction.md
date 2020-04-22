---
title: series_periods_validate() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_periods_validate()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8eba96e21513e776c984a356f88a705ca46485af
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663409"
---
# <a name="series_periods_validate"></a>series_periods_validate()

타임시리즈에 지정된 길이의 주기적 패턴이 포함되어 있는지 확인합니다.  

응용 프로그램의 트래픽을 측정하는 메트릭은 주간 및/또는 일일 기간을 특징으로 하는 경우가 많습니다. 이는 주간 및 `series_periods_validate()` 일일 기간에 대한 검사를 실행하여 확인할 수 있습니다.

이 함수는 시계열의 동적 배열(일반적으로 [make-series](make-seriesoperator.md) 연산자의 결과 출력)을 포함하는 `real` 열과 유효성을 검사할 기간의 길이를 정의하는 하나 이상의 숫자를 입력하는 것으로 사용합니다. 

이 함수는 다음 2개의 열을 출력합니다.
* *기간*: 유효성을 검사할 기간(입력에 제공됨)을 포함하는 동적 배열
* *점수*: *마침표* 배열의 각 위치에서 마침표의 중요성을 측정하는 0과 1 사이의 점수를 포함하는 동적 배열

**구문**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* `,` . . . ] `)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식( 일반적으로 [make-series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력)
* *period1*, *period2*등: `real` 확인 기간을 지정하는 숫자( bin 크기 단위) 예를 들어 계열이 1시간 저장소에 있는 경우 주간 기간은 168개의 저장소입니다.

> [!IMPORTANT]
> * 각 *마침표* 인수에 대한 최소 값은 **4이고** 최대값은 입력 계열 길이의 절반입니다. 이러한 경계 외부의 *마침표* 인수의 경우 출력 점수는 **0이**됩니다.
>
> * 입력 타임계는 정규적이어야 합니다( 즉, 상수 저장소로 집계됨).이 경우 항상 [메이크 시리즈를](make-seriesoperator.md)사용하여 작성된 경우입니다. 그렇지 않은 경우 출력은 의미가 없습니다.
> 
> * 함수는 유효성을 검사하기 위해 최대 16개의 기간을 허용합니다.


**예제**

다음 쿼리는 하루에 두 번 집계된 응용 프로그램 트래픽의 한 달의 스냅숏을 포함합니다(예: 저장소 크기는 12시간).

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="시리즈 기간":::

이 `series_periods_validate()` 시리즈에서 실행하면 주간 기간(14점 길이)을 검증하면 5일 기간(10점)을 확인할 때 **0점만점을** 획득합니다.

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 시리즈\_\_기간은\_\_y 기간의 유효성을 검사합니다.  | 시리즈\_\_기간\_\_y 점수 유효성 검사 |
|-------------|-------------------|
| [14.0, 10.0] | [0.84,0.0]  |