---
title: stdev () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 stdev () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a29621a18a364145585022b1f0651100cadab1c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618543"
---
# <a name="stdev-aggregation-function"></a>stdev () (집계 함수)

그룹 전체에서 *Expr* 의 표준 편차를 계산 하 여 그룹을 [샘플](https://en.wikipedia.org/wiki/Sample_%28statistics%29)로 간주 합니다. 

* 사용 되는 수식:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Stdev 샘플":::

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

**구문**

`stdev(` *식* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용 되는 식입니다. 

**반환**

그룹 전체에 대 한 *Expr* 의 표준 편차 값입니다.
 
**예제**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|