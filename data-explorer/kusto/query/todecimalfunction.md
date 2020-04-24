---
title: todecimal() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 todecimal()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506310"
---
# <a name="todecimal"></a>todecimal()

입력을 소수 자릿수 표현으로 변환합니다.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**구문**

`todecimal(`*Expr*`)`

**인수**

* *예:* 소수로 변환되는 표현식입니다. 

**반환**

변환에 성공하면 결과는 소수 자릿수가 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.
 
*참고*: 가능하면 [real()을](./scalar-data-types/real.md) 사용하는 것을 선호합니다.