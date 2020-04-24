---
title: tostring() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 tostring()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505885"
---
# <a name="tostring"></a>tostring()

입력을 문자열 표현으로 변환합니다.

```kusto
tostring(123) == "123"
```

**구문**

`tostring(`*Expr*`)`

**인수**

* *예견*: 문자열로 변환되는 표현식입니다. 

**반환**

*Expr* 값이 null이 아닌 경우 결과는 *Expr의*문자열 표현이 됩니다.
*Expr* 값이 null이면 결과는 빈 문자열이 됩니다.
 