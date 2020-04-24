---
title: maxif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 maxif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9be25615f9da61aec6b4d56543f624fa0c24c1c4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512447"
---
# <a name="maxif-aggregation-function"></a>maxif() (집계 함수)

*조건어가* 평가하는 그룹 전체의 최대 값을 `true`으로 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

조건자 식 없이 그룹 전체의 최대값을 반환하는 [max()](max-aggfunction.md) 함수를 참조하십시오.

**구문**

`summarize``maxif(` *예시*`,`*조건자*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 
* *술어*: true이면 *Expr* 계산 값이 최대로 검사된다는 조건어입니다.

**반환**

*조건어가* 평가하는 그룹 전체의 `true` *Expr* 최대값입니다.

**예**

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|