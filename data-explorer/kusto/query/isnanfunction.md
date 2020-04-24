---
title: isnan() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 isnan()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513569"
---
# <a name="isnan"></a>isnan()

입력이 NaN(숫자가 아님) 값인지 여부를 반환합니다.  

**구문**

`isnan(`*Ⅹ*`)`

**인수**

* *x*: 실제 숫자입니다.

**반환**

x가 NaN인 경우 0이 아닌 값(true); 그렇지 않으면 0 (거짓) .

**참고 항목**

* 값이 null인지 확인하려면 [isnull()](isnullfunction.md)을 참조하십시오.
* 값이 유한한지 확인하려면 [isfinite()를](isfinitefunction.md)참조하십시오.
* 값이 무한인지 확인하려면 [isinf()를](isinffunction.md)참조하십시오.

**예제**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|이산 (isnan)|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|