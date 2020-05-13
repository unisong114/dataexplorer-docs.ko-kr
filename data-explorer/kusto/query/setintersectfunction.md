---
title: set_intersect ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 set_intersect ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 23b751dce38f5b595ba081c9a29e1b1a5442c96f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372346"
---
# <a name="set_intersect"></a>set_intersect()

`dynamic`모든 배열 (arr1 ∩ arr2 ∩ ...)에 있는 모든 고유 값 집합의 (JSON) 배열을 반환 합니다.

**구문**

`set_intersect(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**인수**

* *arr1 ... arrN*: 교차 집합을 만들기 위한 입력 배열입니다 (적어도 두 개의 배열). 모든 인수는 동적 배열 이어야 합니다 ( [pack_array](packarrayfunction.md)참조). 

**반환**

모든 배열에 있는 모든 고유 값 집합의 동적 배열을 반환 합니다. [`set_union()`](setunionfunction.md)및를 참조 하십시오 [`set_difference()`](setdifferencefunction.md) .

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|열1|
|---|
|[1]|
|sr-2|
|[3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|arr|
|---|
|[]|
