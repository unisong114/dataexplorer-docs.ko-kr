---
title: isnotnull ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isnotnull ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253276"
---
# <a name="isnotnull"></a>isnotnull()

`true`인수가 null이 아닌 경우를 반환 합니다.

## <a name="syntax"></a>구문

`isnotnull(`[*값*]`)`

`notnull(`[*값*] `)` -별칭 `isnotnull`

## <a name="example"></a>예제

```kusto
T | where isnotnull(PossiblyNull) | count
```

참고로 이 효과를 달성하는 다른 방법이 있습니다.

```kusto
T | summarize count(PossiblyNull)
```