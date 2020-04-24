---
title: 분산() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 분산() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b1d2ea47060ecede855a3fb419bbbfe2bf0b538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504712"
---
# <a name="variance-aggregation-function"></a>분산() (집계 함수)

그룹을 [샘플로](https://en.wikipedia.org/wiki/Sample_%28statistics%29)고려하여 그룹 전체의 *Expr* 분산을 계산합니다. 

* 사용된 수식: ![대체 텍스트](./images/aggregations/variance-sample.png "분산 샘플")

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`variance(` *예시* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체의 *Expr* 분산 값입니다.
 
**예**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2.5|