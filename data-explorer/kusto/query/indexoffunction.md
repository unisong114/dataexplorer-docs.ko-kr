---
title: indexof ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 indexof ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e237441d28f12ffc6f27f8a591980a701825e39
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347462"
---
# <a name="indexof"></a>indexof()

입력 문자열에서 맨 처음 발견 되는 지정 된 문자열의 0부터 시작 하는 인덱스를 보고 합니다.

Lookup 또는 input 문자열이 *문자열* 유형이 아닌 경우 함수는 값을 강제로 *문자열*에 캐스팅 합니다.

자세한 내용은 [`indexof_regex()`](indexofregexfunction.md)를 참조하세요.

## <a name="syntax"></a>Syntax

`indexof(`*원본* `,` *조회* `[,` *start_index* `[,` *길이* `[,` *발생*`]]])`

## <a name="arguments"></a>인수

* *원본*: 입력 문자열입니다.  
* *lookup*: 조회할 문자열입니다.
* *start_index*: 시작 위치를 검색 합니다. (선택 사항)
* *length*: 검사할 문자 위치의 수입니다. 값이-1 이면 길이 제한이 없음을 의미 합니다. (선택 사항)
* *발생*횟수: 발생 횟수입니다. 기본값은 1입니다. (선택 사항)

## <a name="returns"></a>반환

*조회*의 인덱스 위치 (0부터 시작)입니다.

는 문자열을 입력에서 찾을 수 없으면-1을 반환 합니다.

관련이 없는 (0 보다 작음) *start_index*, *발생*또는 (-1 미만) *길이* 매개 변수는 *null*을 반환 합니다.

## <a name="examples"></a>예제
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
