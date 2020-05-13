---
title: series_seasonal ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_seasonal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 88160a55ba8342e3ed6bce90ec77c5a370ab7358
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372479"
---
# <a name="series_seasonal"></a>series_seasonal()

검색 되거나 지정 된 계절 기간에 따라 계열의 계절 구성 요소를 계산 합니다.

**구문**

`series_seasonal(`*계열* `[,` *기간*`])`

**인수**

* *계열*: 입력 숫자 동적 배열
* *period* (선택 사항): 각 계절 기간에 있는 Bin의 정수 수, 가능한 값:
    *  -1 (기본값): [series_periods_detect ()](series-periods-detectfunction.md) 를 사용 하 여 기간을 임계값 *0.7*으로 자동 검색 합니다. 계절성가 검색 되지 않으면 0을 반환 합니다.
    * 양의 정수: 계절 구성 요소에 대 한 기간으로 사용 됩니다.
    * 다른 모든 값: 계절성를 무시 하 고 일련의 0을 반환 합니다.

**반환**

계열의 계산 된 계절 구성 요소를 포함 하는 *계열* 입력과 동일한 길이의 동적 배열입니다. 계절 구성 요소는 기간의 bin 위치에 해당 하는 모든 값의 *중앙값* 으로 계산 됩니다.

**참고 항목:**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**예**

**1. 기간 자동 검색**

다음 예제에서 시리즈의 기간이 자동으로 검색 되 고 첫 번째 시리즈의 기간이 6 개의 bin 및 두 번째 5 개의 bin으로 검색 되며, 세 번째 계열의 기간이 너무 짧아서 검색 되지 않으며 일련의 0이 반환 됩니다 (마침표를 강제 적용 하는 방법에 대 한 다음 예제 참조).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2, 5, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1]|[1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0]|
|[8, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10]|[10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]|



**2. 마침표 적용**

다음 예에서는 시리즈의 기간이 너무 짧아서 [series_periods_detect ()](series-periods-detectfunction.md) 에서 검색할 수 없기 때문에 주기적으로 계절 패턴을 가져오기 위해 마침표를 명시적으로 적용 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1, 3, 5, 1, 3, 5, 2, 4, 6]|[1.0, 3.0, 5.0, 1.0, 3.0, 5.0, 1.0, 3.0, 5.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5]|
