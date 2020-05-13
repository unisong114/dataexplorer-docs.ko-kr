---
title: series_divide ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_divide ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7d5bdba030687c17c355eb72ce2fc9c358c10ebd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372842"
---
# <a name="series_divide"></a>series_divide()

두 숫자 계열 입력의 요소 단위 나누기를 계산 합니다.

**구문**

`series_divide(`*series1* `,` *series2*`)`

**인수**

* *series1, series2*: 숫자 배열을 입력 합니다. 첫 번째 요소는 두 번째를 동적 배열 결과로 나눈 값입니다. 모든 인수는 동적 배열 이어야 합니다. 

**반환**

두 입력 간의 계산 된 요소 단위 나누기 작업의 동적 배열입니다. 숫자가 아닌 요소나 비 기존 요소 (크기가 다른 배열)는 `null` 요소 값을 생성 합니다.

참고: 입력이 정수인 경우에도 결과 계열은 double 형식입니다. 0으로 나누기는 이중 나누기 (예: 2/0는 double (+ inf)를 생성)를 따릅니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0.25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0.25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0.25, 1.0, 4.0]|
