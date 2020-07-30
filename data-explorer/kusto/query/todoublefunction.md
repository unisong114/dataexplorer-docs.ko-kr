---
title: todouble ()/toreal ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 todouble ()/toreal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34734f3975b1720c1d009f190d4fae2ebc54283f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350760"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

입력을 형식의 값으로 변환 합니다 `real` . `todouble()`및 `toreal()` 는 동의어입니다.

```kusto
toreal("123.4") == 123.4
```

## <a name="syntax"></a>구문

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 값이 형식의 값으로 변환 되는 식입니다 `real` .

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 형식의 값입니다 `real` .
변환이 실패 하면 결과는 값 `real(null)` 입니다.

*참고*: 가능한 경우 [double () 또는 real ()](./scalar-data-types/real.md) 을 사용 하는 것이 좋습니다.