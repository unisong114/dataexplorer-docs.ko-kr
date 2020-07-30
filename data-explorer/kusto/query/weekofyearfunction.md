---
title: week_of_year ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 week_of_year ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 82678a68166061fc7b8a30c7cb2e019c8d3d9e0c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338561"
---
# <a name="week_of_year"></a>week_of_year ()

주 번호를 나타내는 정수를 반환 합니다. 주 번호는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)에 따라 첫 번째 목요일을 포함 하는 1 년의 첫 번째 주에서 계산 됩니다.

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntax

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>인수

* `a_date`: `datetime`입니다.

## <a name="returns"></a>반환

`week number`-지정 된 날짜를 포함 하는 주 번호입니다.

## <a name="examples"></a>예제

|입력                                    |출력|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`이 함수의 사용 되지 않는 변형입니다. `weekofyear()`ISO 8601 규격이 아닙니다. 1 년의 첫 번째 주는 연도의 첫 번째 주 수요일의 주로 정의 되었습니다.
이 함수의 현재 버전는 `week_of_year()` ISO 8601 규격입니다. 1 년의 첫 번째 주는 연도의 첫 번째 목요일의 주로 정의 됩니다.