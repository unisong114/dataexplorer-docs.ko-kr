---
title: tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741664"
---
# <a name="tostring"></a>tostring()

입력을 문자열 표현으로 변환 합니다.

```kusto
tostring(123) == "123"
```

**구문**

`tostring(`*`Expr`*`)`

**인수**

* *`Expr`*: 문자열로 변환 되는 식입니다. 

**반환**

*`Expr`* 값이 null이 아닌 경우 결과는의 *`Expr`* 문자열 표현이 됩니다.
*`Expr`* 값이 null 이면 결과가 빈 문자열이 됩니다.
 