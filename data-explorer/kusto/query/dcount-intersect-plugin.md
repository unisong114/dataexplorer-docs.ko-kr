---
title: dcount_intersect 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 dcount_intersect 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9c969cafbf2ec38d9d192cf28c7b1e5599f58631
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793433"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect 플러그 인

값을 기준으로 N 집합 `hll` 의 교집합을 계산 하 고 ([2.16] 범위의 n) n 값을 반환 `dcount` 합니다.

지정 된 집합 S<sub>1</sub>, s<sub>2</sub>,.. S<sub>n</sub> 반환 값은의 고유 개수를 나타냅니다.  
S<sub>1,</sub>s<sub>1</sub> ∩ s<sub>2</sub>,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ s<sub>3</sub>,  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ ... ∩ S<sub>n</sub>

```kusto
T | evaluate dcount_intersect(hll_1, hll_2, hll_3)
```

## <a name="syntax"></a>구문

*T* `| evaluate` `dcount_intersect(` *hll_1*, *hll_2*, [ `,` *hll_3* ... `,` ]`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *hll_i*: 함수를 사용 하 여<sub>계산 된</sub> set S의 값입니다 [`hll()`](./hll-aggfunction.md) .

## <a name="returns"></a>반환

N 개 값이 포함 된 테이블을 반환 `dcount` 합니다 (열 마다 교집합을 나타냄).
열 이름은 s0, s1, ... (n-1까지).

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|evenNumbers|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|