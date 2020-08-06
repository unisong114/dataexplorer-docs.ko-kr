---
title: tolong ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 tolong ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804120"
---
# <a name="tolong"></a>tolong()

입력을 long (부호 있는 64 비트) 숫자 표현으로 변환 합니다.

```kusto
tolong("123") == 123
```

> [!NOTE]
> 가능 하면 [long ()](./scalar-data-types/long.md) 을 사용 하는 것이 좋습니다.

## <a name="syntax"></a>구문

`tolong(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: long으로 변환 되는 식입니다. 

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 long 숫자가 됩니다.
변환이 실패 하면 결과는가 됩니다 `null` .
 