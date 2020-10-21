---
title: binary_xor ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 binary_xor ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aecf005556a9997e63ee3547f4e0b23da236cf5d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243472"
---
# <a name="binary_xor"></a>binary_xor()

두 값에 대 한 비트 연산의 결과를 반환 합니다 `xor` .

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>구문

`binary_xor(`*num1* `,` *num2*`)`

## <a name="arguments"></a>인수

* *num1*, *num2*: long 숫자입니다.

## <a name="returns"></a>반환

값 쌍에 대 한 논리적 XOR 연산을 반환 합니다. num1 ^ num2.