---
title: array_split() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_split()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518567"
---
# <a name="array_split"></a>array_split()

분할 인덱스에 따라 배열을 여러 배열로 분할하고 생성된 배열을 동적 배열로 압축합니다.

**구문**

`array_split(`*arr*, *지수*`)`

**인수**

* *arr*: 분할 입력 배열, 동적 배열이어야합니다.
* *인덱스*: 분할 인덱스(0 기준)가 있는 정수 또는 동적 정수 배열은 음수 값이 array_length + 값으로 변환됩니다.

**반환**

범위 [0..i1)의 값을 가진 N+1 배열을 포함하는 동적 배열[i1.. i2), ... [iN.. array_length)에서 Arr, 여기서 N은 입력 인덱스와 i1의 수입니다. iN은 인덱스입니다.

**예**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|도착|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|도착|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|