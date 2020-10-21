---
title: todecimal ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 todecimal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250510"
---
# <a name="todecimal"></a>todecimal()

입력을 10 진수 표현으로 변환 합니다.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>구문

`todecimal(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 10 진수로 변환 되는 식입니다. 

## <a name="returns"></a>반환

변환이 성공 하면 결과는 10 진수입니다.
변환이 실패 하면 결과는가 됩니다 `null` .
 
*참고*: 가능한 경우 [real ()을](./scalar-data-types/real.md) 사용 하는 것이 좋습니다.