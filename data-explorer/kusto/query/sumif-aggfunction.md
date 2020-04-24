---
title: sumif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 sumif() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506667"
---
# <a name="sumif-aggregation-function"></a>sumif() (집계 함수)

*조건어가* 평가하는 `true` *Expr의* 합계를 .

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

조건자 식 없이 행을 합산하는 [sum()](sum-aggfunction.md) 함수를 사용할 수도 있습니다.

**구문**

`sumif(` *사전*`,`*설명서* 요약`)`

**인수**

* *Expr*: 집계 계산에 대한 식입니다. 
* *술어*: true이면 *Expr의*계산된 값이 합계에 추가된다는 조건어를 지정합니다. 

**반환**

*조건어가* 평가하는 `true` *Expr의* 합계 값입니다.

> [!TIP]
> `where filter | summarize sum(expr)` 대신 `summarize sumif(expr, filter)` 사용

**예제**

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