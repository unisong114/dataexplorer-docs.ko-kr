---
title: 천장() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 천장()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2ecd043c43bb1af6530364d200d5dc9db640f95
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517258"
---
# <a name="ceiling"></a>ceiling()

지정된 숫자 식보다 크거나 같을 가장 작은 정수를 계산합니다.

**구문**

`ceiling(`*Ⅹ*`)`

**인수**

* *x*: 실제 숫자입니다.

**반환**

* 가장 작은 정수보다 크거나 같으며 지정된 숫자 식입니다. 

**예**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|