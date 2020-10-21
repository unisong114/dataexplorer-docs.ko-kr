---
title: binary_shift_left ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 binary_shift_left ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f01f0178fa850009f6298446b02c9d4bd913bf8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252609"
---
# <a name="binary_shift_left"></a>binary_shift_left()

숫자 쌍에 대해 이항 시프트 왼쪽 연산을 반환 합니다.

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>구문

`binary_shift_left(`*num1* `,` *num2*`)`

## <a name="arguments"></a>인수

* *num1*, *num2*: int 숫자입니다.

## <a name="returns"></a>반환

숫자 쌍 (num1 <<  (num2% 64)에 대해 이항 시프트 왼쪽 작업을 반환 합니다.
N이 음수 이면 NULL 값이 반환 됩니다.