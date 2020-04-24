---
title: pack_array() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 pack_array()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad13efd6b0743a3c092b2859ea032c3731ffdf1b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511869"
---
# <a name="pack_array"></a>pack_array()

모든 입력 값을 동적 배열로 압축합니다.

**구문**

`pack_array(`*Expr1*`[`` *Expr2*]`, )'

**인수**

* *예시 1... N*: 동적 배열로 압축할 입력 식입니다.

**반환**

Expr1, Expr2, ... , ExprN의 값을 포함하는 동적 배열입니다.

**예제**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|열1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|열1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|