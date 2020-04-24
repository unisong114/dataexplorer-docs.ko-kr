---
title: array_concat() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_concat()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518754"
---
# <a name="array_concat"></a>array_concat()

여러 동적 배열을 단일 배열에 연결합니다.

**구문**

`array_concat(`*arr1*`[`` *arr2*, ...]`, )'

**인수**

* *arr1... arrN*: 동적 배열로 연결될 입력 배열입니다. 모든 인수는 동적 배열여야 [합니다(pack_array](packarrayfunction.md)참조). 

**반환**

arr1, arr2, ...

**예제**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|열1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|