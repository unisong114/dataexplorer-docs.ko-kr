---
title: array_length ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 array_length ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14203e3078b7fe30222ea26320ed1391000d5c05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349553"
---
# <a name="array_length"></a>array_length()

동적 배열의 요소 수를 계산 합니다.

## <a name="syntax"></a>Syntax

`array_length(`*array*`)`

## <a name="arguments"></a>인수

* *array*: `dynamic` 값입니다.

## <a name="returns"></a>반환

*array*의 배열 수 또는 *array*가 배열이 아닌 경우 `null`입니다.

## <a name="examples"></a>예제

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```