---
title: dcount_intersect 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 dcount_intersect 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516136"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect 플러그인

hll 값([2.16] 범위의 N)을 기준으로 N 세트 간의 교차를 계산하고 N dcount 값을 반환합니다.

주어진 세트 S<sub>1,</sub>S<sub>2</sub>, .. S<sub>n</sub> - 반환 값은 다음과 같은 뚜렷한 개수를 나타냅니다.  
S<sub>1,</sub>S<sub>1</sub> 에 S<sub>2</sub>,  
S<sub>1</sub> ~S<sub>2</sub> ~S<sub>3</sub>,  
... ,  
S<sub>1</sub> <sub>~S 2</sub> ~... <sub>(9)</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**구문**

*T* `| evaluate` T `dcount_intersect(` *hll_1*, *hll_2*,`,` *[hll_3* `,` ...]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *hll_i*: [hll()](./hll-aggfunction.md) 함수로 계산된 S<sub>세트 의</sub> 값입니다.

**반환**

N dcount 값(열 열당 교차집합을 나타내는)이 있는 테이블을 반환합니다.
열 이름은 s0, s1, ... (n-1까지).

**예**

```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|짝수|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|