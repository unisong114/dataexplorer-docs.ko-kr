---
title: binary_xor() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_xor()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517530"
---
# <a name="binary_xor"></a>binary_xor()

두 값의 비트별 `xor` 연수 결과를 반환합니다.

```kusto
binary_xor(x,y)
```

**구문**

`binary_xor(`*num1* `,` *num2*`)`

**인수**

* *num1*, *num2*: 긴 숫자.

**반환**

숫자 쌍에 논리적 XOR 작업을 반환합니다: num1 ^ num2.