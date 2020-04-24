---
title: series_subtract() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_subtract()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1e984bc35192da5d61448211c49ff582f225eb19
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507942"
---
# <a name="series_subtract"></a>series_subtract()

두 개의 숫자 계열 입력의 요소별 빼기 계산합니다.

**구문**

`series_subtract(`*시리즈1* `,` *시리즈2*`)`

**인수**

* *series1, series2*: 입력 숫자 배열, 두 번째는 요소-wise가 첫 번째에서 동적 배열 결과로 뺍니다. 모든 인수는 동적 배열이어야 합니다. 

**반환**

계산된 요소별 동적 배열은 두 입력 간에 작업을 뺍니다. 숫자가 아닌 요소 또는 존재하지 않는 요소(다른 크기의 배열)는 `null` 요소 값을 생성합니다.

**예제**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[-3,0,3]|
|[2,4,8]|[8,4,2]|[-6,0,6]|
|[3,6,12]|[12,6,3]|[-9,0,9]|