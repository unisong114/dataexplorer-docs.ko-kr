---
title: tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243809"
---
# <a name="tostring"></a>tostring()

입력을 문자열 표현으로 변환 합니다.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>구문

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>인수

* *`Expr`*: 문자열로 변환 되는 식입니다. 

## <a name="returns"></a>반환

*`Expr`* 값이 null이 아닌 경우 결과는의 문자열 표현이 됩니다 *`Expr`* .
*`Expr`* 값이 null 이면 결과가 빈 문자열이 됩니다.
 