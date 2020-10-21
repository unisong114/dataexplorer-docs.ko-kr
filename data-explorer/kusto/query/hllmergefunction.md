---
title: hll_merge ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hll_merge ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 6bf364106bef8fbe626f96c22502dae748884180
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252971"
---
# <a name="hll_merge"></a>hll_merge()

`hll`결과 (집계 버전의 스칼라 버전)를 병합 [`hll_merge()`](hll-merge-aggfunction.md) 합니다.

[기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

## <a name="syntax"></a>구문

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

## <a name="arguments"></a>인수

* `hll`값이 병합 될 열입니다.

## <a name="returns"></a>반환

열 `*Exrp1*` ,, ...을 `*Expr2*` `*ExprN*` 한 `hll` 값에 병합 하는 결과입니다.

## <a name="examples"></a>예

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
