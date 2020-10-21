---
title: dayofyear ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 dayofyear ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252384"
---
# <a name="dayofyear"></a>dayofyear()

지정 된 연도의 일 수를 나타내는 정수를 반환 합니다.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>구문

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>인수

* `a_date`: `datetime`입니다.

## <a name="returns"></a>반환

`day number` 지정 된 연도의입니다.