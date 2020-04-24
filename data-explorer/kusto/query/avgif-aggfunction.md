---
title: avgif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 avgif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518261"
---
# <a name="avgif-aggregation-function"></a>avgif() (집계 함수)

*조건어가* 평가하는 그룹 전체의 *Expr* [평균을](avg-aggfunction.md) `true`계산합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`avgif(` *사전*`, `*설명서* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 값이 `null` 있는 레코드는 무시되고 계산에 포함되지 않습니다.
* *술어*: true이면 *Expr* 계산 값이 평균에 추가된다는 조건어를 지정합니다.

**반환**

*조건어가* 평가하는 그룹 전체의 *Expr* 평균 `true`값입니다.
 
**예**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|