---
title: hll () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 hll () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: cbe1b0639a0379fe84bc9c100a629bbadd9c3a63
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226570"
---
# <a name="hll-aggregation-function"></a>hll () (집계 함수)

[`dcount`](dcount-aggfunction.md) [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 그룹 전체에 대 한 중간 결과를 계산 합니다.

[기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

**구문**

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

**인수**

* *`Expr`*: 집계 계산에 사용 되는 식입니다. 
* *`Accuracy`*(지정 된 경우)는 속도와 정확도 간의 균형을 제어 합니다.

  |정확도 값 |정확도  |속도  |Error  |
  |---------|---------|---------|---------|
  |`0` | lowest | 빨리 | 1.6% |
  |`1` | default  | 조정이 | 0.8% |
  |`2` | high | slow | 0.4%  |
  |`3` | high | slow | 0.28% |
  |`4` | 매우 높음 | 최저 | 0.2% |
    
**반환**

그룹 전체에서 고유 카운트의 중간 결과입니다 *`Expr`* .
 
**팁**

1. 집계 함수를 사용 하 여 둘 [`hll_merge`](hll-merge-aggfunction.md) 이상의 중간 결과를 병합할 수 있습니다 `hll` ( `hll` 출력 에서만 작동).

1. 함수를 사용 하 여 [`dcount_hll`](dcount-hllfunction.md) `dcount` `hll`  /  집계 함수에서를 계산할 수 있습니다 `hll_merge` .

**예**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024, 14], [-5473486921211236216,-6230876016761372746, 3953448761157777955, 4246796580750024372], []]|
|2007-09-20 21:50:00.0000000|[[1024, 14], [4835649640695509390], []]|
|2007-09-29 08:10:00.0000000|[[1024, 14], [4246796580750024372], []]|
|2007-12-30 16:00:00.0000000|[[1024, 14], [4246796580750024372,-8936707700542868125], []]|
