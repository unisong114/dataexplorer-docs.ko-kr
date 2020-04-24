---
title: tdigest_merge() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 tdigest_merge()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506378"
---
# <a name="tdigest_merge"></a>tdigest_merge()

tdigest 결과 병합 (집계 버전의 [`tdigest_merge()`](tdigest-merge-aggfunction.md)스칼라 버전).

기본 알고리즘(T-다이제스트)과 예상 오차에 대한 자세한 [내용은 여기를 참조하십시오.](percentiles-aggfunction.md#estimation-error-in-percentiles)

**구문**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - 별칭.

**인수**

* 병합할 tdigests가 있는 열입니다.

**반환**

열을 병합하는 결과 `*Expr1*`, ... `*Expr2*` `*ExprN*` 하나의 다이제스트에.

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