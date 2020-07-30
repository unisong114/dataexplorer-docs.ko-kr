---
title: sumif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 sumif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cd9900b5087ed0d6ae7e97d2f2dad809bb909331
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350811"
---
# <a name="sumif-aggregation-function"></a>sumif () (집계 함수)

*조건자* 가로 계산 되는 *Expr* 의 합을 반환 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

조건자 식 없이 행의 합계를 계산 하는 [sum ()](sum-aggfunction.md) 함수를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

`sumif(` *Expr* `,` *조건자* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 대 한 식입니다. 
* *조건자*: 조건자 (true 인 경우)의 경우 *Expr*의 계산 된 값이 합계에 추가 됩니다. 

## <a name="returns"></a>반환

*조건자* 가로 계산 되는 *Expr* 의 sum 값입니다 `true` .

## <a name="example"></a>예제

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|