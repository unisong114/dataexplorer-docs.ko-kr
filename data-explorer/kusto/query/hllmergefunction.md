---
title: hll_merge() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 hll_merge()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514096"
---
# <a name="hll_merge"></a>hll_merge()

hll 결과(집계 버전의 스칼라 버전)를 병합합니다. [`hll_merge()`](hll-merge-aggfunction.md)

[기본 알고리즘 *(H*yper*L*og*Og*og)과 추정 정확도에](dcount-aggfunction.md#estimation-accuracy)대해 읽어보십시오.

**구문**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**인수**

* 병합할 hll 값이 있는 열입니다.

**반환**

열을 병합하는 결과 `*Exrp1*`, ... `*Expr2*` `*ExprN*` 하나의 hll 값으로 이동합니다.

**예**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|