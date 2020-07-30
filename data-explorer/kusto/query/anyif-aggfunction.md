---
title: anyif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 anyif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 23285c0747e7fecbdce810536af195f72f27236f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349723"
---
# <a name="anyif-aggregation-function"></a>anyif () (집계 함수)

는 조건자가 "true" 인 [요약 연산자](summarizeoperator.md)의 각 그룹에 대해 레코드 하나를 임의로 선택 합니다. 함수는 각 레코드에 대 한 식의 값을 반환 합니다.

## <a name="syntax"></a>Syntax

`summarize``anyif` `(` *Expr*, *Predicate*`)`

## <a name="arguments"></a>인수

* *Expr*: 반환할 입력에서 선택한 각 레코드에 대 한 식입니다.
* *조건자*: 평가를 위해 고려할 레코드를 나타내는 조건자입니다.

## <a name="returns"></a>반환

`anyif`집계 함수는 요약 연산자의 각 그룹에서 무작위로 선택 된 각 레코드에 대해 계산 된 식의 값을 반환 합니다. *조건자* 가 "true"를 반환 하는 레코드만 선택할 수 있습니다. 조건자가 "true"를 반환 하지 않으면 null 값이 생성 됩니다.

**주의**

이 함수는 "true" 인 일부 조건자에 따라 복합 그룹 키의 값 마다 하나의 열에 대 한 샘플 값을 가져오려는 경우에 유용 합니다.

이러한 값이 있는 경우 함수는 null이 아닌 값 또는 비어 있지 않은 값을 반환 하려고 합니다.

## <a name="examples"></a>예제

300 ~ 6억 모집단의 임의 대륙을 표시 합니다.

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="모든 1":::
