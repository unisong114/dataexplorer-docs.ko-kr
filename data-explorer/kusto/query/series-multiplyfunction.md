---
title: series_multiply ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_multiply ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1f88cdfc1490f8b00d8104286441e366aaf46f3f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372568"
---
# <a name="series_multiply"></a>series_multiply()

두 숫자 계열 입력의 요소 단위 곱하기를 계산 합니다.

**구문**

`series_multiply(`*series1* `,` *series2*`)`

**인수**

* *series1, series2*: 요소를 동적 배열 결과에 곱하는 입력 숫자 배열입니다. 모든 인수는 동적 배열 이어야 합니다. 

**반환**

두 입력 간의 계산 된 요소 단위 곱하기 작업의 동적 배열입니다. 숫자가 아닌 요소나 비 기존 요소 (크기가 다른 배열)는 `null` 요소 값을 생성 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [4, 4, 4]|
|[2, 4, 8]    |[8, 4, 2]|   [16, 16, 16]|
|[3, 6, 12]   |[12, 6, 3]|  [36, 36, 36]|
