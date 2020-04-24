---
title: dcount_hll() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 dcount_hll()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516153"
---
# <a name="dcount_hll"></a>dcount_hll()

hll [결과(hll](hll-aggfunction.md) 또는 [hll_merge](hll-merge-aggfunction.md)의해 생성된)에서 dcount를 계산합니다.

[기본 알고리즘 *(H*yper*L*og*Og*og)과 추정 정확도에](dcount-aggfunction.md#estimation-accuracy)대해 읽어보십시오.

**구문**

`dcount_hll(`*Expr*`)`

**인수**

* *예시*: [hll](hll-aggfunction.md) 또는 [hll 병합에](hll-merge-aggfunction.md) 의해 생성된 표현식

**반환**

*Expr의* 각 값의 고유 개수

**예**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|