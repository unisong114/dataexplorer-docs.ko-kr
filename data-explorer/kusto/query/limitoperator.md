---
title: limit 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 제한 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e30bc631af2d055d7cf8c2720a60637f4b76f8cb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246580"
---
# <a name="limit-operator"></a>limit 연산자

지정한 수 만큼의 행을 반환 합니다.

```kusto
T | limit 5
```

**별칭**

[take 연산자](takeoperator.md)