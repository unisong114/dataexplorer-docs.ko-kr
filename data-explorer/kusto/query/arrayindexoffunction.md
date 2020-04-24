---
title: array_index_of() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_index_of()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518618"
---
# <a name="array_index_of"></a>array_index_of()

지정된 항목에 대한 배열을 검색하고 해당 위치를 반환합니다.

**구문**

`array_index_of(`*배열*,*값*`)`

**인수**

* *배열*: 검색할 입력 배열입니다.
* *값*: 검색할 값입니다. 값은 긴, 정수, 이중, 날짜 시간, 시간 범위, 소수점, 문자열 또는 guid 형식이어야 합니다.

**반환**

조회의 제로 기반 인덱스 위치입니다.
배열에서 값을 찾을 수 없는 경우 -1을 반환합니다.

**예제**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|결과|
|---|
|3|

**참고 항목**

값이 배열에 있는지 여부만 확인하려는 경우 해당 위치에 관심이 없는 경우 [set_has_element(arr, value)를](sethaselementfunction.md) 사용할 수 있습니다.