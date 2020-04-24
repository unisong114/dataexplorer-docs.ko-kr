---
title: toint() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 toint()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506106"
---
# <a name="toint"></a>toint()

입력을 정수(서명된 32비트) 숫자 표현으로 변환합니다.

```kusto
toint("123") == 123
```

**구문**

`toint(`*Expr*`)`

**인수**

* *예절*: 정수로 변환되는 표현식입니다. 

**반환**

변환에 성공하면 결과는 정수 번호가 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.
 
*참고*: 가능하면 [int()를](./scalar-data-types/int.md) 사용하는 것을 선호합니다.