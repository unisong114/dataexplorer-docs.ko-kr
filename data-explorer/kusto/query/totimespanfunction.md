---
title: totimespan() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 totimespan()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505868"
---
# <a name="totimespan"></a>totimespan()

입력을 [시간 범위](./scalar-data-types/timespan.md) 스칼라로 변환합니다.

```kusto
totimespan("0.00:01:00") == time(1min)
```

**구문**

`totimespan(`*Expr*`)`

**인수**

* *예시*: [시간 범위로](./scalar-data-types/timespan.md)변환되는 표현식입니다. 

**반환**

전환이 성공하면 결과는 [시간 범위](./scalar-data-types/timespan.md) 값이 됩니다.
변환에 성공하지 못하면 결과는 null이 됩니다.
 