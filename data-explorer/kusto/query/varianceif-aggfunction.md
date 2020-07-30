---
title: varianceif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 varianceif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338476"
---
# <a name="varianceif-aggregation-function"></a>varianceif () (집계 함수)

*조건자* 가로 계산 되는 그룹에서 *Expr* 의 [분산](variance-aggfunction.md) 을 계산 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`varianceif(` *Expr* `, ` *조건자* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 
* *Predicate*: True 이면 *식* 계산 된 값이 분산에 추가 됩니다.

## <a name="returns"></a>반환

*조건자* 가로 평가 되는 그룹 전체에 대 한 *Expr* 의 가변성 값 `true` 입니다.
 
## <a name="examples"></a>예제

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|