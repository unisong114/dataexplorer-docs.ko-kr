---
title: max () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 max () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cd53f0701064d95ab2d088e774f49b06f8d323f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251062"
---
# <a name="max-aggregation-function"></a>max () (집계 함수)

그룹 전체에서 최대값을 반환 합니다. 

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 

## <a name="returns"></a>반환

그룹 전체에서 *Expr* 의 최대값입니다.
 
> [!TIP]
> 이렇게 하면 최소값 또는 최대값이 제공 됩니다 (예: 최고 또는 최저 가격).
> 그러나 행에 다른 열이 필요한 경우 (예: 가격이 가장 낮은 공급 업체의 이름- [arg_max](arg-max-aggfunction.md) 또는 [arg_min](arg-min-aggfunction.md).