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
ms.openlocfilehash: 314244c58eca6082b9042b263e6b3e6faeb69840
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351202"
---
# <a name="set_has_element"></a>set_has_element()

지정 된 집합에 지정 된 요소가 포함 되어 있는지 여부를 확인 합니다.

## <a name="syntax"></a>Syntax

`set_has_element(`*배열*,*값*`)`

## <a name="arguments"></a>인수

* *array*: 검색할 입력 배열입니다.
* *값*: 검색할 값입니다. 값은,,,,,, 또는 형식 이어야 합니다 `long` `integer` `double` `datetime` `timespan` `decimal` `string` `guid` .

## <a name="returns"></a>반환

배열에 값이 있는지 여부에 따라 True 또는 false입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|결과|
|---|
|1|

**참고 항목**

[`array_index_of(arr, value)`](arrayindexoffunction.md)를 사용 하 여 배열에 값이 있는 위치를 찾습니다. 두 함수는 모두 동일 하 게 작동 합니다.
