---
title: maxif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 maxif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251716"
---
# <a name="maxif-aggregation-function"></a>maxif () (집계 함수)

*조건자* 가로 계산 되는 그룹 전체에서 최대값을 반환 합니다 `true` .

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

조건자 식을 사용 하지 않고 그룹 전체에서 최대값을 반환 하는- [max ()](max-aggfunction.md) 함수를 참조 하세요.

## <a name="syntax"></a>구문

`summarize``maxif(` *Expr* `,` *조건자*`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 
* *Predicate*: True 이면 *Expr* 계산 된 값이 최대값을 확인 하는 조건자입니다.

## <a name="returns"></a>반환

*조건자* 가로 계산 되는 그룹 전체의 *Expr* 의 최대값입니다 `true` .

## <a name="examples"></a>예

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|