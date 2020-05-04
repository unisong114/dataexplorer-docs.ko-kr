---
title: tdigest_merge ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 tdigest_merge ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737728"
---
# <a name="tdigest_merge"></a>tdigest_merge()

결과 `tdigest` (집계 버전 [`tdigest_merge()`](tdigest-merge-aggfunction.md)의 스칼라 버전)를 병합 합니다.

기본 알고리즘 (T-다이제스트) 및 예상 되는 오류에 대 한 자세한 내용은 [여기](percentiles-aggfunction.md#estimation-error-in-percentiles)를 참조 하세요.

**구문**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* Expr2`, ...)` -별칭입니다.

**인수**

* `tdigest` 값이 병합 될 열입니다.

**반환**

열 `*Expr1*` `*Expr2*`병합 결과 (...) `*ExprN*` 하나 `tdigest`입니다.

**예**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|