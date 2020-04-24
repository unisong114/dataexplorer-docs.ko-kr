---
title: array_slice() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_slice()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518584"
---
# <a name="array_slice"></a>array_slice()

동적 배열의 조각을 추출합니다.

**구문**

`array_slice(`*arr*, *시작*, *끝*]`)`

**인수**

* *arr*: 슬라이스를 추출할 입력 어레이는 동적 배열이어야 합니다.
* *시작*: 슬라이스의 0 기반(포함) 시작 인덱스, 음수 값은 array_length+start로 변환됩니다.
* *end*: 슬라이스의 0기반(포함) 끝 인덱스, 음수 값은 array_length+end로 변환됩니다.

참고: 범위를 벗어난 인덱스는 무시됩니다.

**반환**

범위 [start.]에 있는 값의 동적 배열입니다. arr에서 종료합니다.

**예**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|도착|슬라이스|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|도착|슬라이스|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|도착|슬라이스|
|---|---|
|[1,2,3,4,5]|[3,4]|