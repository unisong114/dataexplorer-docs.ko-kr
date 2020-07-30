---
title: any () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 모든 () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 73c3a660dc7a34f1f9fef840b13f47c13b4d1b2f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349740"
---
# <a name="any-aggregation-function"></a>any () (집계 함수)

임의로 [요약 연산자](summarizeoperator.md)의 각 그룹에 대해 하나의 레코드를 선택 하 고 각 레코드에 대해 하나 이상의 식의 값을 반환 합니다.

## <a name="syntax"></a>Syntax

`summarize``any` `(` (*Expr* [ `,` *Expr2* ...]) | `*``)`

## <a name="arguments"></a>인수

* *Expr*: 반환할 입력에서 선택한 각 레코드에 대 한 식입니다.
* *Expr2* . *Exprn*: 추가 식입니다.

## <a name="returns"></a>반환

`any`집계 함수는 각 레코드에 대해 계산 된 식의 값을 반환 합니다 .이 값은 요약 연산자의 각 그룹에서 무작위로 선택 됩니다.

`*`인수가 제공 되는 경우 함수는 식이 group by 열 (있는 경우)을 제한 하는 요약 연산자에 대 한 입력의 모든 열인 것 처럼 동작 합니다.

**주의**

이 함수는 복합 그룹 키 값 마다 하나 이상의 열에 대 한 샘플 값을 가져오려는 경우에 유용 합니다.

단일 열 참조를 사용 하 여 함수를 제공 하면 해당 값이 있는 경우 null이 아닌 값 또는 비어 있지 않은 값을 반환 하려고 시도 합니다.

이 함수의 임의 특성의 결과로 연산자의 단일 응용 프로그램에서이 함수를 여러 번 사용 하는 것 `summarize` 은 여러 식이 포함 된 단일 시간을 사용 하는 것과 동일 하지 않습니다. 이전에는 각 응용 프로그램이 다른 레코드를 선택 하 고, 후자는 모든 값이 단일 레코드 (고유 그룹별)를 통해 계산 되도록 보장 합니다.

## <a name="examples"></a>예제

임의 대륙 표시:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="모든 1":::

임의의 레코드에 대 한 모든 세부 정보 표시:

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="모든 2":::

각 무작위 대륙의 세부 정보를 모두 표시 합니다.

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="모든 3":::
