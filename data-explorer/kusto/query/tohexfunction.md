---
title: tohex ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 tohex ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7784424d0053761d4cfdb373dea0ed57f8bca83e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250372"
---
# <a name="tohex"></a>tohex()

입력을 16 진수 문자열로 변환 합니다.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>구문

`tohex(`*Expr* `, [` , ` *MinLength*]` ) '

## <a name="arguments"></a>인수

* *Expr*: 16 진수 문자열로 변환 될 정수 또는 long 값입니다.  다른 형식은 지원 되지 않습니다.
* *MinLength*: 출력에 포함할 선행 문자 수를 나타내는 숫자 값입니다.  1에서 16 사이의 값이 지원 되며 16 보다 큰 값은 16으로 잘립니다.  문자열이 선행 문자 없이 minLength 보다 길면 minLength가 효과적으로 무시 됩니다.  음수는 기본 데이터 크기 만큼 최소로만 표현할 수 있으므로 int (32 비트)의 경우 minLength는 최소 8이 고 long (64 비트)의 경우 최소 16입니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 문자열 값이 됩니다.
변환이 실패 하면 결과는 null이 됩니다.