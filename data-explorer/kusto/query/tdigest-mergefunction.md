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
ms.openlocfilehash: f85c2c45ff4e69ba59f2a13313c8c2ac494c56a6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340992"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest`결과 (집계 버전의 스칼라 버전)를 병합 [`tdigest_merge()`](tdigest-merge-aggfunction.md) 합니다.

기본 알고리즘 (T-다이제스트) 및 예상 되는 오류에 대 한 자세한 내용은 [여기](percentiles-aggfunction.md#estimation-error-in-percentiles)를 참조 하세요.

## <a name="syntax"></a>Syntax

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` -별칭입니다.

## <a name="arguments"></a>인수

* `tdigest`값이 병합 될 열입니다.

## <a name="returns"></a>반환

열 `*Expr1*` ,, ...을 `*Expr2*` `*ExprN*` 1로 병합 하는 결과 `tdigest` 입니다.

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
