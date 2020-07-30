---
title: set_intersect ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 set_intersect ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 709da983599b4e8b0c8b06cf7bff4276ba03b5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351168"
---
# <a name="set_intersect"></a>set_intersect()

`dynamic`모든 배열 (arr1 ∩ arr2 ∩ ...)에 있는 모든 고유 값 집합의 배열을 반환 합니다.

## <a name="syntax"></a>Syntax

`set_intersect(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>인수

* *arr1 ... arrN*: 교차 집합을 만들기 위한 입력 배열입니다 (적어도 두 개의 배열). 모든 인수는 동적 배열 이어야 합니다. 자세한 내용은 [pack_array](packarrayfunction.md)를 참조 하세요. 

## <a name="returns"></a>반환

모든 배열에 있는 모든 고유 값 집합의 동적 배열을 반환 합니다. [`set_union()`](setunionfunction.md)및를 참조 하십시오 [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|열1|
|---|
| [1]|
|sr-2|
|[3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|arr|
|---|
|[]|
