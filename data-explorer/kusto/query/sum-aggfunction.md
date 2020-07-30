---
title: sum () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 sum () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3053a2c3bd423a2e1b8a2690bcdf54de9f1a1e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350828"
---
# <a name="sum-aggregation-function"></a>sum () (집계 함수)

그룹 전체에서 *Expr* 의 합계를 계산 합니다. 

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`sum(` *식* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. 

## <a name="returns"></a>반환

그룹 전체에서 *Expr* 의 합계 값입니다.
 