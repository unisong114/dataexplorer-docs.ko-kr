---
title: arg_min () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 arg_min () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 33e2657f2569957002d17d7061cfec863402027e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349689"
---
# <a name="arg_min-aggregation-function"></a>arg_min () (집계 함수)

그룹에서 *ExprToMinimize*를 최소화 하는 행을 찾은 다음 *ExprToReturn* 값을 반환 하거나 `*` 전체 행을 반환 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`summarize`[ `(` *NameExprToMinimize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_min` `(` *ExprToMinimize*, `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>인수

* *ExprToMinimize*: 집계 계산에 사용 되는 식입니다. 
* *ExprToReturn*: *ExprToMinimize* 가 minimum 인 경우 값을 반환 하는 데 사용 되는 식입니다. 반환 되는 식은 입력 테이블의 모든 열을 반환 하는 와일드 카드 (*) 일 수 있습니다.
* *NameExprToMinimize*: *ExprToMinimize*을 나타내는 결과 열의 선택적 이름입니다.
* *NameExprToReturn*: *ExprToReturn*을 나타내는 결과 열의 추가 선택적 이름입니다.

## <a name="returns"></a>반환

그룹에서 *ExprToMinimize*를 최소화 하는 행을 찾은 다음 *ExprToReturn* 값을 반환 하거나 `*` 전체 행을 반환 합니다.

## <a name="examples"></a>예

각 제품의 가장 저렴 한 공급 업체 표시:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

공급자 이름 뿐만 아니라 모든 세부 정보를 표시 합니다.

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

각 대륙에서 국가를 사용 하 여 southernmost 도시를 찾습니다.

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="인수 최소값":::
