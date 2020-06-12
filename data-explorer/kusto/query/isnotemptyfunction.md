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
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717345"
---
# <a name="isnotempty"></a>isnotempty()

`true`인수가 빈 문자열이 아니며 null이 아닌 경우를 반환 합니다.

```kusto
isnotempty("") == false
```

**구문**

`isnotempty(`[*값*]`)`

`notempty(`[*값*] `)` --별칭`isnotempty`
