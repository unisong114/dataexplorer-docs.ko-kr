---
title: tobool ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 tobool ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350794"
---
# <a name="tobool"></a>tobool()

입력을 부울 (부호 있는 8 비트) 표현으로 변환 합니다.

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>Syntax

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` 앤티앨리어스

## <a name="arguments"></a>인수

* *Expr*: boolean으로 변환 되는 식입니다. 

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과가 부울이 됩니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .
