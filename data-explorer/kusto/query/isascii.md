---
title: isascii ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isascii ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347292"
---
# <a name="isascii"></a>isascii()

`true`인수가 유효한 ascii 문자열이 면을 반환 합니다.
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>Syntax

`isascii(`[*값*]`)`

## <a name="returns"></a>반환

인수가 유효한 ascii 문자열 인지 여부를 나타냅니다.

## <a name="example"></a>예제

```kusto
T
| where isascii(fieldName)
| count
```