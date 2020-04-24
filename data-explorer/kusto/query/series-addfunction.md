---
title: series_add() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_add()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ea8c391060e487413a166c236abf789edcba0a0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509064"
---
# <a name="series_add"></a>series_add()

두 개의 숫자 계열 입력의 요소별 추가를 계산합니다.

**구문**

`series_add(`*시리즈1* `,` *시리즈2*`)`

**인수**

* *series1, series2*: 동적 배열 결과에 요소가 추가되는 입력 숫자 배열입니다. 모든 인수는 동적 배열이어야 합니다. 

**반환**

계산된 요소별 동적 배열은 두 입력 간에 작업을 추가합니다. 숫자가 아닌 요소 또는 존재하지 않는 요소(다른 크기의 배열)는 `null` 요소 값을 생성합니다.

**예제**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[5,4,5]|
|[2,4,8]|[8,4,2]|[10,8,10]|
|[3,6,12]|[12,6,3]|[15,12,15]|