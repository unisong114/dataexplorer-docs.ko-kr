---
title: 인덱오프() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 indexof()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513943"
---
# <a name="indexof"></a>indexof()

함수는 입력 문자열 내에서 지정된 문자열이 처음 발생하는 0기반 인덱스를 보고합니다.

조회 또는 입력 문자열이 문자열 형식이 아닌 경우 강제로 문자열에 값을 캐스팅합니다.

을 [`indexof_regex()`](indexofregexfunction.md)참조하십시오.

**구문**

`indexof(`*소스*`,`*조회*`[,`*start_index*`[,`*길이*`[,`*발생*`]]])`

**인수**

* *소스*: 입력 문자열.  
* *조회*: 문자열을 찾습니다.
* *start_index*: 검색 시작 위치(선택 사항).
* *길이*: 검사할 문자 위치 수, -1 무제한 길이 정의(선택 사항).
* *발생*: 발생 기본 값 1(선택 사항)입니다.

**반환**

*조회의*제로 기반 인덱스 위치.

입력에서 문자열을 찾을 수 없는 경우 -1을 반환합니다.

관련이 없는 경우(0 미만) *start_index*start_index *발생* 또는 (-1 미만) *길이* 매개 변수 - *null을*반환합니다.

**예**
```kusto
print
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
