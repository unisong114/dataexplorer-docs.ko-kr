---
title: tobool() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 tobool()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506361"
---
# <a name="tobool"></a>tobool()

입력을 부울(서명된 8비트) 표현으로 변환합니다.

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**구문**

`tobool(`*익스프레(별칭)*
`toboolean(`*Expr* `)``)`

**인수**

* *예절*: 부울로 변환되는 표현식입니다. 

**반환**

변환에 성공하면 결과는 부울이 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.
 