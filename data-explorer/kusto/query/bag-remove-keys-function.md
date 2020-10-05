---
title: bag_remove_keys ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_remove_keys ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712350"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ()

속성 모음에서 키와 연관 된 값을 제거 `dynamic` 합니다.

## <a name="syntax"></a>Syntax

`bag_remove_keys(`*모음* `, ` *키*`)`

## <a name="arguments"></a>인수

* *모음*: `dynamic` 속성 모음 입력입니다.
* *키*: `dynamic` 배열에는 입력에서 제거할 키가 포함 되어 있습니다. 키는 속성 모음의 첫 번째 수준을 나타냅니다.
중첩 수준에서 키를 지정 하는 것은 지원 되지 않습니다.

## <a name="returns"></a>반환

`dynamic`지정 된 키와 해당 값이 없는 속성 모음을 반환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|input|result|
|---|---|
|{<br>  "key1": 123,<br>  "key2": "abc"<br>}|{<br>  "key1": 123<br>}|
|{<br>  "key1": "value",<br>  "key3": 42.0<br>}|{<br>  "key1": "value",<br>  "key3": 42.0<br>}|
