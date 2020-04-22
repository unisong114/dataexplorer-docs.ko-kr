---
title: arg_min() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 arg_min(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663979"
---
# <a name="arg_min-aggregation-function"></a>arg_min() (집계 함수)

*ExprToMinimize를*최소화하는 그룹에서 행을 찾아 *ExprToReturn(또는* `*` 전체 행을 반환)의 값을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize`[`(`*네임엑스프토리메라카리메라카* `,` *NameExprToReturn* `,` `)=` `arg_min` ] `(` *ExprtoMinimize*, `*`  |  *ExprtoReturn* [...]`,``)`

**인수**

* *ExprToMinimize*: 집계 계산에 사용되는 표현식입니다. 
* *ExprToReturn*: *ExprToMinimize가* 최소일 때 값을 반환하는 데 사용되는 표현식입니다. 반환할 표현식은 입력 테이블의 모든 열을 반환하는 와일드카드(*)일 수 있습니다.
* *NameExprToMinimize*: *ExprToMinimize를*나타내는 결과 열에 대한 선택적 이름입니다.
* *NameExprToReturn*: *ExprToReturn을*나타내는 결과 열에 대한 추가 선택적 이름 .

**반환**

*ExprToMinimize를*최소화하는 그룹에서 행을 찾아 *ExprToReturn(또는* `*` 전체 행을 반환)의 값을 반환합니다.

**예**

각 제품의 가장 저렴한 공급 업체를 표시 :

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

공급업체 이름뿐만 아니라 모든 세부 정보를 표시합니다.

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

각 대륙에서 가장 남쪽에 위치한 도시를 찾아보세요.

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/aggregations/arg-min.png" alt-text="아르그 민":::
