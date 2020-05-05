---
title: hll_merge ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hll_merge ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741783"
---
# <a name="hll_merge"></a>hll_merge()

결과 `hll` (집계 버전 [`hll_merge()`](hll-merge-aggfunction.md)의 스칼라 버전)를 병합 합니다.

[기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

**구문**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**인수**

* `hll` 값이 병합 될 열입니다.

**반환**

열 `*Exrp1*` `*Expr2*`병합 결과 (...) `*ExprN*` 하나의 `hll` 값으로

**예**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|