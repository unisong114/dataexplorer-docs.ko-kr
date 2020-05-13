---
title: series_add ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_add ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b5847fec10eb76a6fe5a139809766d2a3ca4f089
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372912"
---
# <a name="series_add"></a>series_add()

두 숫자 계열 입력의 요소 덧셈을 계산 합니다.

**구문**

`series_add(`*series1* `,` *series2*`)`

**인수**

* *series1, series2*: 동적 배열 결과에 요소를 추가 하는 데 사용할 숫자 배열을 입력 합니다. 모든 인수는 동적 배열 이어야 합니다. 

**반환**

두 입력 간의 계산 된 요소 단위 추가 작업의 동적 배열입니다. 숫자가 아닌 요소나 비 기존 요소 (크기가 다른 배열)는 `null` 요소 값을 생성 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5, 4, 5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3, 6, 12]|[12, 6, 3]|[15, 12, 15]|
