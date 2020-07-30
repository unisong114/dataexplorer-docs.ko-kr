---
title: avg () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 avg () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349400"
---
# <a name="avg-aggregation-function"></a>avg () (집계 함수)

그룹 전체에서 *Expr* 의 평균을 계산 합니다. 

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`avg(` *식* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다. `null`값이 있는 레코드는 무시 되며 계산에 포함 되지 않습니다.

## <a name="returns"></a>반환

그룹 전체에 대 한 평균 *Expr* 값입니다.
 