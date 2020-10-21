---
title: array_length ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 array_length ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246785"
---
# <a name="array_length"></a>array_length()

동적 배열의 요소 수를 계산 합니다.

## <a name="syntax"></a>구문

`array_length(`*array*`)`

## <a name="arguments"></a>인수

* *array*: `dynamic` 값입니다.

## <a name="returns"></a>반환

*array*의 배열 수 또는 *array*가 배열이 아닌 경우 `null`입니다.

## <a name="examples"></a>예

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```