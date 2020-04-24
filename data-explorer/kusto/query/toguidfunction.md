---
title: toguid() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 toguid()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506140"
---
# <a name="toguid"></a>toguid()

입력을 표현으로 [`guid`](./scalar-data-types/guid.md) 변환합니다.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**구문**

`toguid(`*Expr*`)`

**인수**

* *예:* 스칼라로 [`guid`](./scalar-data-types/guid.md) 변환되는 표현식입니다. 

**반환**

변환에 성공하면 결과는 스칼라가 [`guid`](./scalar-data-types/guid.md) 됩니다.
변환에 성공하지 못하면 결과가 `null`됩니다.

*참고*: 가능하면 [guid()를](./scalar-data-types/guid.md) 사용하는 것을 선호합니다.