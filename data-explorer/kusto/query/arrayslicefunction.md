---
title: array_slice ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_slice ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: f2de8d91b73a3495c8b0902d710bd87c7fecbafa
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737558"
---
# <a name="array_slice"></a>array_slice()

동적 배열의 조각을 추출 합니다.

**구문**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**인수**

* *`arr`*: 조각을 추출 하는 입력 배열은 동적 배열 이어야 합니다.
* *`start`*: 0부터 시작 하는 (포함) 조각의 시작 인덱스입니다. 음수 값은 array_length + start로 변환 됩니다.
* *`end`*: 0부터 시작 하는 (포함) 조각의 끝 인덱스입니다. 음수 값은 array_length + end로 변환 됩니다.

참고: 범위를 벗어난 인덱스는 무시 됩니다.

**반환**

의 [`start..end`] 범위에 있는 값의 동적 배열 `arr`입니다.

**예**


```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|계층이|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|계층이|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4]|