---
title: array_split ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_split ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 102077c9c1116bd9476c6dae59d993a6379b69bd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225558"
---
# <a name="array_split"></a>array_split()

분할 된 인덱스에 따라 배열을 여러 배열로 분할 하 고 생성 된 배열을 동적 배열로 압축 합니다.

**구문**

`array_split`(*`arr`*, *`indices`*)

**인수**

* *`arr`*: 분할할 입력 배열입니다. 동적 배열 이어야 합니다.
* *`indices`*: 분할 된 인덱스 (0부터 시작)를 포함 하는 정수 또는 정수 배열의 동적 배열입니다. 음수 값은 array_length + 값으로 변환 됩니다.

**반환**

의 범위에 있는 값이 포함 된 N + 1 배열을 포함 하는 동적 배열입니다 `[0..i1), [i1..i2), ... [iN..array_length)` `arr` . 여기서 n은 입력 인덱스의 수이 고 `i1...iN` 인덱스입니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1, 2], [3, 4, 5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1], [2, 3], [4, 5]]|
