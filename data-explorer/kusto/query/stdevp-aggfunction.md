---
title: stdevp() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 stdevp(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0549c15ec9e2435d242f210e6dfc2163796e5f39
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663200"
---
# <a name="stdevp-aggregation-function"></a>stdevp(집계 함수)

그룹을 [모집단으로](https://en.wikipedia.org/wiki/Statistical_population)고려하여 그룹 전체의 *Expr* 표준 편차를 계산합니다. 

* 사용된 수식:

:::image type="content" source="images/aggregations/stdev-population.png" alt-text="스트데프 인구":::

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`stdevp(` *예시* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체의 *Expr표준* 편차 값입니다.
 
**예**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.4142135623731|