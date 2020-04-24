---
title: set_difference() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 set_difference()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507806"
---
# <a name="set_difference"></a>set_difference()

첫 `dynamic` 번째 배열에 있지만 다른 배열에 없는 모든 고유 값 집합의 (JSON) 배열을 반환합니다 ( ((arr1 \ arr2) \ arr3 \...).

**구문**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**인수**

* *arr1... arrN*: 차이 집합(적어도 두 개의 배열)을 만들기 위한 입력 배열입니다. 모든 인수는 동적 배열여야 [합니다(pack_array](packarrayfunction.md)참조). 

**반환**

arr1에 있지만 다른 배열에는 없는 모든 고유 값 집합의 동적 배열을 반환합니다. 및 [`set_union()`](setunionfunction.md) [`set_intersect()`](setintersectfunction.md).

**예제**

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
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|도착|
|---|
|[]|