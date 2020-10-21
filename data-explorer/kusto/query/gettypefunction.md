---
title: gettype ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 gettype ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8fc1c3949ef13e504de6ba76be1bd5e600926288
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244809"
---
# <a name="gettype"></a>gettype()

단일 인수의 런타임 형식을 반환 합니다.

런타임 형식은 명목상 형식이 인 식의 명목상 (정적) 형식과 다를 수 있습니다 `dynamic` .이 경우 `gettype()` 실제 값의 기능은 형식 (값이 메모리에 인코딩 되는 방법)을 표시 하는 데 유용할 수 있습니다.

## <a name="syntax"></a>구문

`gettype(`*Expr*`)`

## <a name="returns"></a>반환

단일 인수의 런타임 형식을 나타내는 문자열입니다.

## <a name="examples"></a>예

|식                          |반환      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|