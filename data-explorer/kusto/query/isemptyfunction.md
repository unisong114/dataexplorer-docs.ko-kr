---
title: isempty ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isempty ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251723"
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