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
ms.openlocfilehash: 98ed69475232dde474f21d1f4a7f792c82a69637
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225618"
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

의 [] 범위에 있는 값의 동적 배열 `start..end` `arr` 입니다.

**예**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|계층이|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|계층이|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4]|
