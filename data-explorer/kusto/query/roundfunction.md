---
title: 라운드() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 round()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510237"
---
# <a name="round"></a>round()

반올림된 소스를 지정된 정밀도로 반환합니다.

**구문**

`round(`*출처* `,` [ *정밀도*]`)`

**인수**

* *출처*: 소스 스칼라 라운드가 계산됩니다.
* *정밀도*: 소스가 반올림되는 숫자 수입니다. (기본값은 0)

**반환**

지정된 정밀도로 반올림된 소스입니다.

라운드는 첫 [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) 번째 라운드가 특정 숫자의 숫자로 반올림하고 마지막 라운드 값은 지정된 bin 크기의 정수 배수(라운드(2.15, 1)가 2.2를 반환하고 bin(2.15, 1)은 2를 반환한다는 것과 다릅니다.
 

**예**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```