---
title: make_set () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_set () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e6eb481423e31e4dfa1b4e6c738ffb525e9aaef7
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618407"
---
# <a name="make_set-aggregation-function"></a>make_set () (집계 함수)

*Expr*이 그룹에서 가지는 고유 값 집합의 `dynamic` (JSON) 배열을 반환합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

**구문**

`summarize``make_set(` *Expr* [`,` *MaxSize*]`)`

**인수**

* *Expr*: 집계 계산에 대 한 식입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

> [!NOTE]
> 이 함수의 기존 변형 및 사용 되지 않는 변형 `makeset()` : 기본 제한은 *MaxSize* = 128입니다.

**반환**

*Expr*이 그룹에서 가지는 고유 값 집합의 `dynamic` (JSON) 배열을 반환합니다.
배열의 정렬 순서가 정의 되어 있지 않습니다.

> [!TIP]
> 고유 값만 계산 하려면 [dcount ()](dcount-aggfunction.md) 를 사용 합니다.

**예제**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Makeset":::

**참고 항목**

* 반대쪽 [`mv-expand`](./mvexpandoperator.md) 함수에 연산자를 사용 합니다.
* [`make_set_if`](./makesetif-aggfunction.md)연산자는 조건자를 `make_set`수락 한다는 점을 제외 하 고와 비슷합니다.