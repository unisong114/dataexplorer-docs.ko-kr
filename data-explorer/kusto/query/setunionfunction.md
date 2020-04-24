---
title: set_union() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 set_union()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507670"
---
# <a name="set_union"></a>set_union()

모든 `dynamic` 배열에 있는 모든 고유 값 집합의 (JSON) 배열을 반환합니다( arr1...).

**구문**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**인수**

* *arr1... arrN*: 유니언 세트(적어도 두 개의 배열)를 만드는 입력 배열입니다. 모든 인수는 동적 배열여야 [합니다(pack_array](packarrayfunction.md)참조). 

**반환**

모든 배열에 있는 모든 고유 값 집합의 동적 배열을 반환합니다. 및 [`set_intersect()`](setintersectfunction.md) [`set_difference()`](setdifferencefunction.md).

**예제**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|열1|
|---|
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|