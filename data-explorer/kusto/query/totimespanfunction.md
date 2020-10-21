---
title: totimespan ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 totimespan ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243751"
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
