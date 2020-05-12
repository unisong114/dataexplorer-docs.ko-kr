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
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226553"
---
# <a name="hll_merge"></a>hll_merge()

`hll`결과 (집계 버전의 스칼라 버전)를 병합 [`hll_merge()`](hll-merge-aggfunction.md) 합니다.

[기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

**구문**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**인수**

* `hll`값이 병합 될 열입니다.

**반환**

열 `*Exrp1*` ,, ...을 `*Expr2*` `*ExprN*` 한 `hll` 값에 병합 하는 결과입니다.

**예**

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
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
