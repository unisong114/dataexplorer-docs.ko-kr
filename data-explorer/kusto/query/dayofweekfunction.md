---
title: dayofweek ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 dayofweek ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722034"
---
# <a name="dayofweek"></a>dayofweek()

이전 일요일 이후의 날짜 수를로 반환 합니다 `timespan` .

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
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
