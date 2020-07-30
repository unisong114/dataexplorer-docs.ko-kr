---
title: stdevp () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 stdevp () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c5dffc8695df466dfc1ac9f0c5bcc4a40f687b2a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342719"
---
# <a name="stdevp-aggregation-function"></a>stdevp () (집계 함수)

그룹 전체에서 *Expr* 의 표준 편차를 계산 하 여 그룹을 [모집단](https://en.wikipedia.org/wiki/Statistical_population)으로 간주 합니다. 

* 사용 되는 수식:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Stdev 모집단":::

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`stdevp(` *식* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 

## <a name="returns"></a>반환

그룹 전체에 대 한 *Expr* 의 표준 편차 값입니다.
 
## <a name="examples"></a>예제

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|