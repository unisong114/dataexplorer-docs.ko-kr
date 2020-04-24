---
title: week_of_year() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 week_of_year()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504542"
---
# <a name="week_of_year"></a>week_of_year()

주 번호를 나타내는 정수를 반환합니다. [ISO 8601에](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)따르면 주 번호는 첫 번째 목요일을 포함하는 연도의 첫 주부터 계산됩니다.

```kusto
week_of_year(datetime("2015-12-14"))
```

**구문**

`week_of_year(`*a_date*`)`

**인수**

* `a_date`: `datetime`입니다.

**반환**

`week number`- 지정된 날짜를 포함하는 주 번호입니다.

**예**

|입력                                    |출력|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`이 함수의 사용되지 않는 변형입니다. `weekofyear()`ISO 8601을 준수하지 않았습니다. 연도의 첫 번째 주는 연도의 첫 번째 수요일이 있는 주로 정의되었습니다.
이 함수의 `week_of_year()`현재 버전은 ISO 8601을 준수합니다. 연도의 첫 번째 주는 연도의 첫 번째 목요일이 있는 주로 정의됩니다.