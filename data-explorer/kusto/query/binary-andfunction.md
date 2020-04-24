---
title: binary_and() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_and()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517683"
---
# <a name="binary_and"></a>binary_and()

두 값 간의 비트별 `and` 작업 결과를 반환합니다.

```kusto
binary_and(x,y) 
```

**구문**

`binary_and(`*num1* `,` *num2*`)`

**인수**

* *num1*, *num2*: 긴 숫자.

**반환**

숫자 쌍(num1 & num2)에서 논리AND 연산을 반환합니다.