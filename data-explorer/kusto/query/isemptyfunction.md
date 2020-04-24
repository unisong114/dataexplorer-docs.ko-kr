---
title: isempty() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 empty()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513620"
---
# <a name="isempty"></a>isempty()

인수가 빈 문자열이거나 null인 경우 반환합니다. `true`
    
```kusto
isempty("") == true
```

**구문**

`isempty(`*[값]*`)`

**반환**

인수가 빈 문자열인지 아니면 null인지를 나타냅니다.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|파세이슨("{}")|false

**예제**

```kusto
T
| where isempty(fieldName)
| count
```