---
title: set_has_element ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 set_has_element ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 9cf2ec4371f4aeef8a68cb65fb2b946b9c393054
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372366"
---
# <a name="set_has_element"></a>set_has_element()

지정 된 집합에 지정 된 요소가 포함 되어 있는지 여부를 확인 합니다.

**구문**

`set_has_element(`*배열*,*값*`)`

**인수**

* *array*: 검색할 입력 배열입니다.
* *값*: 검색할 값입니다. 값은,,,,,, 또는 형식 이어야 합니다 `long` `integer` `double` `datetime` `timespan` `decimal` `string` `guid` .

**반환**

배열에 값이 있는지 여부에 따라 True 또는 false입니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|결과|
|---|
|1|

**참고 항목**

배열에 값이 있는 위치에도 관심이 있는 경우 [array_index_of (arr, value)](arrayindexoffunction.md)를 사용할 수 있습니다. 두 함수는 성능 면에서 동일 합니다.
