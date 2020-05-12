---
title: array_concat ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_concat ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 681178cc12d145b1c574357e87ae4f7b33d736c4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225652"
---
# <a name="array_concat"></a>array_concat()

여러 동적 배열을 단일 배열에 연결 합니다.

**구문**

`array_concat(`*arr1* `[` , ` *arr2*, ...]` ) '

**인수**

* *arr1 ... arrN*: 동적 배열에 연결 되는 입력 배열입니다. 모든 인수는 동적 배열 이어야 합니다 ( [pack_array](packarrayfunction.md)참조). 

**반환**

Arr1, arr2, ..., arrN를 사용 하는 배열의 동적 배열입니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|열1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2, 4]|
|[3, 6, 12, 3, 6]|
