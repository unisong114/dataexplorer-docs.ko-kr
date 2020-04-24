---
title: tolong() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 tolong()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506089"
---
# <a name="tolong"></a>tolong()

입력을 긴(서명된 64비트) 숫자 표현으로 변환합니다.

```kusto
tolong("123") == 123
```

**구문**

`tolong(`*Expr*`)`

**인수**

* *예시*: 길게 변환되는 표현식입니다. 

**반환**

변환에 성공하면 결과는 긴 숫자가 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.
 
*참고*: 가능하면 [long()을](./scalar-data-types/long.md) 사용하는 것을 선호합니다.