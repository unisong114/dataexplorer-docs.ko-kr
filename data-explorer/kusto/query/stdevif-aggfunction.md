---
title: stdevif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 stdevif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 831011bcd72c2fc9b06c3c68c6ebda7929d49e4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242533"
---
# <a name="stdevif-aggregation-function"></a>stdevif () (집계 함수)

*조건자* 가로 계산 되는 그룹 전체에 대 한 *Expr* 의 [stdev](stdev-aggfunction.md) 를 계산 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`stdevif(` *Expr* `, ` *조건자* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 
* *Predicate*: True 이면 *Expr* 계산 된 값이 표준 편차에 추가 됩니다.

## <a name="returns"></a>반환

*조건자* 가로 계산 되는 그룹에 대 한 *Expr* 의 표준 편차 값 `true` 입니다.
 
## <a name="examples"></a>예

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|