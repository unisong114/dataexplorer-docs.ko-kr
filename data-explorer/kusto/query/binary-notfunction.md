---
title: binary_not() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_not()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ae808d3d9964b8e63053ed40d65d08f39adf6668
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517666"
---
# <a name="binary_not"></a>binary_not()

입력 값의 비트 부정을 반환합니다.

```kusto
binary_not(x)
```

**구문**

`binary_not(`*숫자1*`)`

**인수**

* *num1*: 숫자 

**반환**

숫자: num1에서 논리적 NOT 작업을 반환합니다.