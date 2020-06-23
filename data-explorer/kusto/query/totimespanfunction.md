---
title: totimespan ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 totimespan ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129042"
---
# <a name="totimespan"></a>totimespan()

입력을 [timespan](./scalar-data-types/timespan.md) 스칼라로 변환 합니다.

```kusto
totimespan("0.00:01:00") == time(1min)
```

**구문**

`totimespan(Expr)`

**인수**

* *`Expr`*: [Timespan](./scalar-data-types/timespan.md)으로 변환 되는 식입니다.

**반환**

성공적으로 변환 되 면 결과는 [timespan](./scalar-data-types/timespan.md) 값이 됩니다.
그렇지 않으면 결과는 null이 됩니다.
