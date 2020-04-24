---
title: 주간() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 dayofweek()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31d3f525653f6e0979229e4355cdec6cb76833f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516306"
---
# <a name="dayofweek"></a>dayofweek()

이전 일요일 이후의 정수 수를 으로 반환합니다. `timespan`

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**구문**

`dayofweek(`*a_date*`)`

**인수**

* `a_date`: `datetime`입니다.

**반환**

이전 일요일부터 시작되는 자정 이후의 `timespan` 이며, 일 수의 낮은 쪽 정수로 내림됩니다.

**예**

```kusto
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```