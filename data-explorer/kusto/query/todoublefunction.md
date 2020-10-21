---
title: todouble ()/toreal ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 todouble ()/toreal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250471"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

입력을 형식의 값으로 변환 합니다 `real` . `todouble()`및 `toreal()` 는 동의어입니다.

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> 가능 하면 [double () 또는 real ()](./scalar-data-types/real.md) 을 사용 하는 것이 좋습니다.

## <a name="syntax"></a>구문

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 값이 형식의 값으로 변환 되는 식입니다 `real` .

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 형식의 값입니다 `real` .
변환이 실패 하면 결과는 값 `real(null)` 입니다.
