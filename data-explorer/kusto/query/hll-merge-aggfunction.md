---
title: hll_merge () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 hll_merge () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 59c6f6a11b108cf6e74ceb59d3483ea1a95f7002
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512387"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (집계 함수)

`HLL`그룹 전체에 대 한 결과를 단일 `HLL` 값으로 병합 합니다.

* [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

자세한 내용은 [기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)를 참조 하세요.

**구문**

`summarize``hll_merge(` *Expr*`)`

**인수**

* `*Expr*`: 집계 계산에 사용 되는 식입니다.

**반환**

함수는 `hll` 그룹 전체에서의 병합 된 값을 반환 합니다 `*Expr*` .
 
**팁**

1) 함수 [dcount_hll] (dcount-hllfunction.md)를 사용 하 여 `dcount` from `hll`  /  `hll-merge` 집계 함수를 계산 합니다.
