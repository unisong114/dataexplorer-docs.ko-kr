---
title: set_difference ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 set_difference ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372388"
---
# <a name="set_difference"></a>set_difference()

`dynamic`첫 번째 배열에 있지만 다른 배열에 없는 모든 고유 값 집합의 (JSON) 배열을 반환 합니다. ((((arr1 \ arr2) \ arr3) \ ...)

**구문**

`set_difference(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**인수**

* *arr1 ... arrN*: 차이 집합을 만들기 위한 입력 배열입니다 (적어도 두 개의 배열). 모든 인수는 동적 배열 이어야 합니다 ( [pack_array](packarrayfunction.md)참조). 

**반환**

Arr1에 있지만 다른 배열에는 없는 모든 고유 값 집합의 동적 배열을 반환 합니다. [`set_union()`](setunionfunction.md)및를 참조 하십시오 [`set_intersect()`](setintersectfunction.md) .

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|열1|
|---|
|[4]|
|20cm(8|
|10|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|arr|
|---|
|[]|