---
title: minif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 minif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512362"
---
# <a name="minif-aggregation-function"></a>minif() (집계 함수)

*조건어가* 평가하는 그룹 전체에서 최소 값을 `true`로 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

조건자 식 없이 그룹 전체에서 최소 값을 반환 하는 [min()](min-aggfunction.md) 함수를 참조 합니다.

**구문**

`summarize``minif(` *예시*`,`*조건자*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.
* *술어*: true이면 *Expr* 계산 값이 최소로 검사된다는 조건어입니다.

**반환**

*조건어가* 평가하는 그룹 전체의 *Expr* 의 `true`최소 값입니다.

**예**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|