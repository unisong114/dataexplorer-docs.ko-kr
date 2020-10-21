---
title: isutf8 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isutf8 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247348"
---
# <a name="isutf8"></a>isutf8()

`true`인수가 유효한 utf8 문자열이 면을 반환 합니다.
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>구문

`isutf8(`[*값*]`)`

## <a name="returns"></a>반환

인수가 유효한 utf8 문자열 인지 여부를 나타냅니다.

## <a name="example"></a>예제

```kusto
T
| where isutf8(fieldName)
| count
```