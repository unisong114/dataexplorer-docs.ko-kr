---
title: array_index_of ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_index_of ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: a366120428d14c713b18aee6652460817c75433a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349570"
---
# <a name="array_index_of"></a>array_index_of()

배열에서 지정 된 항목을 검색 하 여 해당 위치를 반환 합니다.

## <a name="syntax"></a>구문

`array_index_of(`*배열*,*값*`)`

## <a name="arguments"></a>인수

* *array*: 검색할 입력 배열입니다.
* *값*: 검색할 값입니다. 값은 long, integer, double, datetime, timespan, decimal, string 또는 guid 형식 이어야 합니다.

## <a name="returns"></a>반환

조회의 인덱스 위치 (0부터 시작)입니다.
배열에서 값을 찾을 수 없으면-1을 반환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|결과|
|---|
|3|

**참고 항목**

배열에 값이 있는지 여부를 확인 하려는 경우 해당 위치에 관심이 없으면 [set_has_element ( `arr` , `value` )](sethaselementfunction.md)를 사용할 수 있습니다. 이 함수를 통해 쿼리 가독성을 향상 시킬 수 있습니다. 두 함수는 동일한 성능을 갖습니다.
