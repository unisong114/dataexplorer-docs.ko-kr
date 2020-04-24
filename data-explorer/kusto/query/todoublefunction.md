---
title: 토더블()/토리얼() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 todouble()/toreal()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506327"
---
# <a name="todoubletoreal"></a>토더블()/토리얼()

입력을 type `real`값으로 변환합니다. (동의어입니다.)`todouble()` `toreal()`

```kusto
toreal("123.4") == 123.4
```

**구문**

`toreal(`*예시*`)`
`todouble(`*전*`)`

**인수**

* *예:* 값이 형식값으로 `real`변환되는 식입니다.

**반환**

변환에 성공하면 결과는 type의 `real`값입니다.
변환에 성공하지 못하면 결과가 값입니다. `real(null)`

*참고*: 가능하면 [double() 또는 real()를](./scalar-data-types/real.md) 사용하는 것을 선호합니다.