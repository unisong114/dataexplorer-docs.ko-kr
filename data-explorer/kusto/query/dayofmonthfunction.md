---
title: dayofmonth() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 dayofmonth()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516340"
---
# <a name="dayofmonth"></a>dayofmonth()

지정된 월의 일 수를 나타내는 정수 번호를 반환합니다.

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**구문**

`dayofmonth(`*a_date*`)`

**인수**

* `a_date`: `datetime`입니다.

**반환**

`day number`한 달동안