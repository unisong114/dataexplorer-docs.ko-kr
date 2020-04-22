---
title: any() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 모든() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0726b185c22cd84c93e28601a823a35d9685d96d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663955"
---
# <a name="any-aggregation-function"></a>any() (집계 함수)

[요약 연산자의](summarizeoperator.md)각 그룹에 대해 임의로 하나의 레코드를 선택하고 이러한 각 레코드에 대해 하나 이상의 식값을 반환합니다.

**구문**

`summarize``any` *Expr* `,` *Expr2* (Expr [Expr2 ...]) | `(` `*``)`

**인수**

* *예:* 반환할 입력에서 선택한 각 레코드에 대한 식입니다.
* *Expr2* .. *ExprN*: 추가 표현식입니다.

**반환**

집계 `any` 함수는 요약 연산자의 각 그룹에서 임의로 선택한 각 레코드에 대해 계산된 식의 값을 반환합니다.

인수가 `*` 제공되면 함수는 식이 그룹별 열을 금지하는 요약 연산자(있는 경우)에 대한 입력의 모든 열인 것처럼 작동합니다.

**주의**

이 함수는 복합 그룹 키의 값당 하나 이상의 열의 샘플 값을 얻으려는 경우에 유용합니다.

함수가 단일 열 참조와 함께 제공되면 이러한 값이 있는 경우 null/non-empty 값을 반환하려고 시도합니다.

이 함수의 임의 특성으로 인해 `summarize` 연산자의 단일 응용 프로그램에서 여러 번 사용하는 것은 여러 식을 사용하여 한 번 사용하는 것과 동일하지 않습니다. 전자는 각 응용 프로그램이 다른 레코드를 선택할 수 있지만 후자는 모든 값이 단일 레코드(고유 그룹당)를 통해 계산되도록 보장합니다.

**예**

랜덤 대륙 표시:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggregations/any1.png" alt-text="모든 1":::

임의 레코드에 대한 모든 세부 정보를 표시합니다.

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggregations/any2.png" alt-text="모든 2":::

각 임의 대륙에 대한 모든 세부 정보를 표시합니다.

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggregations/any3.png" alt-text="Any 3":::
