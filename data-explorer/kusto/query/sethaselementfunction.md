---
title: set_has_element() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 set_has_element()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507772"
---
# <a name="set_has_element"></a>set_has_element()

지정된 집합에 지정된 요소가 포함되어 있는지 여부를 확인합니다.

**구문**

`set_has_element(`*배열*,*값*`)`

**인수**

* *배열*: 검색할 입력 배열입니다.
* *값*: 검색할 값입니다. `long`값은 형식 , `integer`" `double` `datetime`, `timespan` `decimal`" `string`, `guid`또는 .

**반환**

값이 배열에 있는지 여부에 따라 true 또는 false입니다.

**예제**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|결과|
|---|
|1|

**참고 항목**

값이 배열에 있는 위치에도 관심이 있는 경우 [array_index_of(arr, 값)을](arrayindexoffunction.md)사용할 수 있습니다. 두 함수는 성능면에서 동일합니다.