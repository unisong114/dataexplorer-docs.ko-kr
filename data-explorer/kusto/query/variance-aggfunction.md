---
title: variance () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 가변성 () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618051"
---
# <a name="variance-aggregation-function"></a>variance () (집계 함수)

그룹을 [샘플](https://en.wikipedia.org/wiki/Sample_%28statistics%29)로 간주 하 여 그룹 전체에 대 한 *Expr* 의 분산을 계산 합니다. 

* 사용 되는 수식:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Variance 샘플":::

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

**구문**

`variance(` *식* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용 되는 식입니다. 

**반환**

그룹 전체에 대 한 *Expr* 의 가변성 값입니다.
 
**예제**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|