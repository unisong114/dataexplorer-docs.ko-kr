---
title: avgif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 avgif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 587af53de774332db70ef9bffcadf74d9e2c069d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349383"
---
# <a name="avgif-aggregation-function"></a>avgif () (집계 함수)

*조건자* 가로 계산 되는 그룹 전체의 *Expr* [평균](avg-aggfunction.md) 을 계산 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`avgif(` *Expr* `, ` *조건자* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. `null`값이 있는 레코드는 무시 되며 계산에 포함 되지 않습니다.
* *Predicate*: True 이면 *Expr* 계산 된 값이 평균에 추가 됩니다.

## <a name="returns"></a>반환

*조건자* 가로 평가 되는 그룹의 평균 *Expr* 값 `true` 입니다.
 
## <a name="examples"></a>예

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|