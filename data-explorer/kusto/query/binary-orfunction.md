---
title: binary_or() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_or()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517598"
---
# <a name="binary_or"></a>binary_or()

두 값의 비트별 `or` 연수 결과를 반환합니다. 

```kusto
binary_or(x,y)
```

**구문**

`binary_or(`*num1* `,` *num2*`)`

**인수**

* *num1*, *num2*: 긴 숫자.

**반환**

한 쌍의 숫자에서 논리OR 연산을 반환합니다: num1 | num2.