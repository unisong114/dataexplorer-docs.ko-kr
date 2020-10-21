---
title: sqrt ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 sqrt ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242513"
---
# <a name="sqrt"></a>sqrt()

제곱근 함수를 반환 합니다.  

## <a name="syntax"></a>구문

`sqrt(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수 >= 0.

## <a name="returns"></a>반환

* `sqrt(x) * sqrt(x) == x`
* 인수가 음수이거나 `real` 값으로 변환할 수 없는 경우 `null`입니다. 