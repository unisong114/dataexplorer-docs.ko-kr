---
title: isutf8 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isutf8 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347156"
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