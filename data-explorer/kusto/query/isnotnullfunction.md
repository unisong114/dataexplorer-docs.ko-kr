---
title: isnotnull ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isnotnull ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347190"
---
# <a name="isnotnull"></a>isnotnull()

`true`인수가 null이 아닌 경우를 반환 합니다.

## <a name="syntax"></a>Syntax

`isnotnull(`[*값*]`)`

`notnull(`[*값*] `)` -별칭`isnotnull`

## <a name="example"></a>예제

```kusto
T | where isnotnull(PossiblyNull) | count
```

참고로 이 효과를 달성하는 다른 방법이 있습니다.

```kusto
T | summarize count(PossiblyNull)
```