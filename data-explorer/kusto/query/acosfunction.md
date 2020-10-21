---
title: acos ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 acos ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 486be5487071fa281765de410fe4e5be1ce62a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253118"
---
# <a name="acos"></a>acos()

코사인이 지정 된 숫자 (의 역 연산) 인 각도를 반환 [`cos()`](cosfunction.md) 합니다.

## <a name="syntax"></a>구문

`acos(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: [-1, 1] 범위의 실수입니다.

## <a name="returns"></a>반환

* 의 아크 코사인 값입니다. `x`
* `null``x`<-1 또는 `x` > 1