---
title: tobool ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 tobool ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250583"
---
# <a name="tobool"></a>tobool()

입력을 부울 (부호 있는 8 비트) 표현으로 변환 합니다.

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>구문

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` 앤티앨리어스

## <a name="arguments"></a>인수

* *Expr*: boolean으로 변환 되는 식입니다. 

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과가 부울이 됩니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .
