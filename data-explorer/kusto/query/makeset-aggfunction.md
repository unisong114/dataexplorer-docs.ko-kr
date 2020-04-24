---
title: make_set() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_set(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512685"
---
# <a name="make_set-aggregation-function"></a>make_set() (집계 함수)

*Expr*이 그룹에서 가지는 고유 값 집합의 `dynamic` (JSON) 배열을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_set(` *Expr* `,` [ *최대 크기*]`)`

**인수**

* *Expr*: 집계 계산에 대한 표현식입니다.
* *MaxSize는* 반환되는 최대 요소 수에 대한 선택적 정수 제한입니다(기본값은 *1048576).* MaxSize 값은 1048576을 초과할 수 없습니다.

> [!NOTE]
> 이 함수의 레거시 및 사용되지 `makeset()` 않는 변형: *MaxSize* = 128의 기본 제한이 있습니다.

**반환**

*Expr*이 그룹에서 가지는 고유 값 집합의 `dynamic` (JSON) 배열을 반환합니다.
배열의 정렬 순서가 정의되지 않습니다.

> [!TIP]
> 고유 값만 계산하려면 [dcount()](dcount-aggfunction.md)

**예제**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![대체 텍스트](./images/aggregations/makeset.png "makeset")

**참고 항목**

* 반대 [`mv-expand`](./mvexpandoperator.md) 함수에 연산자 를 사용합니다.
* [`make_set_if`](./makesetif-aggfunction.md)연산자는 `make_set`조건자도 수락하는 경우를 제외하고는 과 유사합니다.