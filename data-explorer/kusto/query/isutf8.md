---
title: isutf8() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 isutf8()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513399"
---
# <a name="isutf8"></a>isutf8()

인수가 유효한 utf8 문자열인 경우 반환합니다. `true`
    
```kusto
isutf8("some string") == true
```

**구문**

`isutf8(`*[값]*`)`

**반환**

인수가 유효한 utf8 문자열인지 여부를 나타냅니다.

**예제**

```kusto
T
| where isutf8(fieldName)
| count
```