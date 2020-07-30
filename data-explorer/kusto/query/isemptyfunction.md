---
title: isempty ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isempty ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347275"
---
# <a name="isempty"></a>isempty()

`true`인수가 빈 문자열 이거나 null 이면를 반환 합니다.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>구문

`isempty(`[*값*]`)`

## <a name="returns"></a>반환

인수가 빈 문자열인지 아니면 null인지를 나타냅니다.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson (" {} ")|false

## <a name="example"></a>예제

```kusto
T
| where isempty(fieldName)
| count
```