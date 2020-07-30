---
title: isnotempty ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 isnotempty ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347207"
---
# <a name="isnotempty"></a>isnotempty()

`true`인수가 빈 문자열이 아니며 null이 아닌 경우를 반환 합니다.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntax

`isnotempty(`[*값*]`)`

`notempty(`[*값*] `)` --별칭`isnotempty`
