---
title: stdevif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 stdevif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506973"
---
# <a name="stdevif-aggregation-function"></a>stdevif() (집계 함수)

*조건어가* 평가하는 그룹 전체에서 `true` *Expr의* [stdev를](stdev-aggfunction.md) 계산합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`stdevif(` *사전*`, `*설명서* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 
* *술어*: true이면 *Expr* 계산 값이 표준 편차에 추가된다는 조건어를 지정합니다.

**반환**

*조건어가* 을 평가하는 그룹 전체의 `true` *Expr* 표준 편차 값입니다.
 
**예**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|