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
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340142"
---
# <a name="totimespan"></a>totimespan()

입력을 [timespan](./scalar-data-types/timespan.md) 스칼라로 변환 합니다.

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>구문

`totimespan(Expr)`

## <a name="arguments"></a>인수

* *`Expr`*: [Timespan](./scalar-data-types/timespan.md)으로 변환 되는 식입니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 [timespan](./scalar-data-types/timespan.md) 값이 됩니다.
그렇지 않으면 결과는 null이 됩니다.
