---
title: tdigest_merge () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 tdigest_merge () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250637"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (집계 함수)

그룹 전체에서 tdigest 결과를 병합 합니다. 

* [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

기본 알고리즘 (T-다이제스트) 및 예상 되는 오류에 대 한 자세한 내용은 [여기](percentiles-aggfunction.md#estimation-error-in-percentiles)를 참조 하세요.

## <a name="syntax"></a>구문

`tdigest_merge(` *Expr* `)` 을 요약 합니다.

`tdigest_merge(` *Expr* `)` -별칭을 요약 합니다.

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 

## <a name="returns"></a>반환

그룹에 대해 *Expr* 의 병합 된 tdigest 값입니다.
 

**팁**

1) [] (Percentile-tdigestfunction.md) 함수를 사용할 수 있습니다 `percentile_tdigest()` .

2) 동일한 그룹에 포함 된 모든 tdigests는 동일한 유형 이어야 합니다.