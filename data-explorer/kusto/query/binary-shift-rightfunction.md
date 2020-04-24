---
title: binary_shift_right() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_shift_right()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517547"
---
# <a name="binary_shift_right"></a>binary_shift_right()

숫자 쌍에 이진 시프트 오른쪽 작업을 반환합니다.

```kusto
binary_shift_right(x,y) 
```

**구문**

`binary_shift_right(`*num1* `,` *num2*`)`

**인수**

* *num1*, *num2*: 긴 숫자.

**반환**

숫자 쌍에 이진 시프트 오른쪽 작업을 반환: num1 >>  (num2%64).
n이 음수이면 NULL 값이 반환됩니다.