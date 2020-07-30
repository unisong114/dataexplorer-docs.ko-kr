---
title: bag_keys ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_keys ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4ce05f536b8903810739c6aa7780f9eaf72c4f87
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349332"
---
# <a name="bag_keys"></a>bag_keys()

동적 속성 모음 개체의 모든 루트 키를 열거 합니다.

`bag_keys(`*동적 개체*`)`

## <a name="returns"></a>반환

키의 배열, 순서는 결정 되지 않습니다.

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(index:long, d:dynamic) [
1, dynamic({'a':'b', 'c':123}), 
2, dynamic({'a':'b', 'c':{'d':123}}),
3, dynamic({'a':'b', 'c':[{'d':123}]}),
4, dynamic(null),
5, dynamic({}),
6, dynamic('a'),
7, dynamic([])]
| extend keys = bag_keys(d)
```

|인덱스|일|키|
|---|---|---|
|1|{<br>  "a": "b",<br>  "c": 123<br>}|[<br>  "a",<br>  "c"<br>]|
|2|{<br>  "a": "b",<br>  "c": {<br>    "d": 123<br>  }<br>}|[<br>  "a",<br>  "c"<br>]|
|3|{<br>  "a": "b",<br>  "c": [<br>    {<br>      "d": 123<br>    }<br>  ]<br>}|[<br>  "a",<br>  "c"<br>]|
|4|||
|5|{}|[]|
|6|a||
|7|[]||
