---
title: gettype ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 gettype ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0efa07b7a1b050fe81ce2f369e8df5af4c05e212
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347666"
---
# <a name="gettype"></a>gettype()

단일 인수의 런타임 형식을 반환 합니다.

런타임 형식은 명목상 형식이 인 식의 명목상 (정적) 형식과 다를 수 있습니다 `dynamic` .이 경우 `gettype()` 실제 값의 기능은 형식 (값이 메모리에 인코딩 되는 방법)을 표시 하는 데 유용할 수 있습니다.

## <a name="syntax"></a>Syntax

`gettype(`*Expr*`)`

## <a name="returns"></a>반환

단일 인수의 런타임 형식을 나타내는 문자열입니다.

## <a name="examples"></a>예제

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