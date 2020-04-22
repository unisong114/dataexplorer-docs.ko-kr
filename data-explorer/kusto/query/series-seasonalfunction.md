---
title: series_seasonal() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_seasonal()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 559731ab7dca2e49e124a856ca7a66a912583b62
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663125"
---
# <a name="series_seasonal"></a>series_seasonal()

감지되거나 지정된 계절 기간에 따라 계열의 계절 구성 요소를 계산합니다.

**구문**

`series_seasonal(`*시리즈* `[,` *기간*`])`

**인수**

* *시리즈*: 입력 숫자 동적 배열
* *기간(선택* 사항): 각 계절 기간의 저장소 정수 수, 가능한 값:
    *  -1(기본값): 임계값이 *0.7인* [series_periods_detect()를](series-periods-detectfunction.md) 사용하여 마침표 자동 감지, 계절성이 감지되지 않으면 0을 반환합니다.
    * 양수 정수: 계절 구성 요소의 기간으로 사용됩니다.
    * 기타 값: 계절성을 무시하고 일련의 제로를 반환합니다.

**반환**

계열의 계산된 계절 구성요소를 포함하는 *계열* 입력과 동일한 길이의 동적 배열입니다. 계절 구성 요소는 마침표 전체의 저장소 위치에 해당하는 모든 값의 *중앙값으로* 계산됩니다.

**다음 을 참조하십시오.**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**예**

**1. 자동 기간을 감지**

다음 예제에서는 시리즈의 기간이 자동으로 감지되고, 첫 번째 시리즈의 기간은 6개의 저장소와 두 번째 5개의 저장소로 감지되고, 세 번째 계열의 기간은 너무 짧아서 감지할 수 없으며 일련의 영점(기간을 강제하는 방법에 대한 다음 예제 참조)을 반환합니다.

```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1]|[1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0]|
|[8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]|[10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0]|



**2. 마침표 를 강제**

다음 예제에서는 시리즈의 기간이 너무 짧아서 [series_periods_detect()](series-periods-detectfunction.md) 에서 검색할 수 없으므로 계절 패턴을 얻기 위해 마침표를 명시적으로 강제로 사용합니다.

```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1,3,5,1,3,5,2,4,6]|[1.0,3.0,5.0,1.0,3.0,5.0,1.0,3.0,5.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5]|
