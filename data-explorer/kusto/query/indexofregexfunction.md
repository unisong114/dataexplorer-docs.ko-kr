---
title: indexof_regex() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 indexof_regex()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513892"
---
# <a name="indexof_regex"></a>indexof_regex()

함수는 입력 문자열 내에서 지정된 문자열이 처음 발생하는 0기반 인덱스를 보고합니다. 일반 문자열 일치는 겹치지 않습니다. 

을 [`indexof()`](indexoffunction.md)참조하십시오.

**구문**

`indexof_regex(`*소스*`,`*조회*`[,`*start_index*`[,`*길이*`[,`*발생*`]]])`

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
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5
|----|----|----|----|----
|0   |3   |-1  |-1  |    