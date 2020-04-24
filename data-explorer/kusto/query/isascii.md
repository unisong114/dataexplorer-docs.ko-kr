---
title: isascii() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 isascii()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513722"
---
# <a name="isascii"></a>isascii()

인수가 유효한 ascii 문자열인 경우 반환합니다. `true`
    
```kusto
isascii("some string") == true
```

**구문**

`isascii(`*[값]*`)`

**반환**

인수가 유효한 ascii 문자열인지 여부를 나타냅니다.

**예제**

```kusto
T
| where isascii(fieldName)
| count
```