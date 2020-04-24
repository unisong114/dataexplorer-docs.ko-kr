---
title: 월간() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 월별()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512345"
---
# <a name="monthofyear"></a>monthofyear()

지정된 연도의 월 수를 나타내는 정수 번호를 반환합니다.

또 다른 별칭: getmonth()

```kusto
monthofyear(datetime("2015-12-14"))
```

**구문**

`monthofyear(`*a_date*`)`

**인수**

* `a_date`: `datetime`입니다.

**반환**

`month number`연도의.