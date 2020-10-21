---
title: isnotempty ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 isnotempty ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253219"
---
# <a name="isnotempty"></a>isnotempty()

`true`인수가 빈 문자열이 아니며 null이 아닌 경우를 반환 합니다.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>구문

`isnotempty(`[*값*]`)`

`notempty(`[*값*] `)` --별칭 `isnotempty`

## <a name="example"></a>예제

```kusto
T
| where isnotempty(fieldName)
| count
```
