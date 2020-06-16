---
title: indexof_regex ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 indexof_regex ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72797b54c3ba431b4a846f9e9661e9693359cceb
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780459"
---
# <a name="indexof_regex"></a>indexof_regex()

함수는 입력 문자열에서 맨 처음 발견 되는 지정 된 문자열의 0부터 시작 하는 인덱스를 보고 합니다. 일반 문자열 일치는 겹치지 않습니다.

[`indexof()`](indexoffunction.md)을 참조하세요.

**구문**

`indexof_regex(`*원본* `,` *조회* `[,` *start_index* `[,` *길이* `[,` *발생*`]]])`

**인수**

|인수     | Description                                     |필수 또는 선택|
|--------------|-------------------------------------------------|--------------------|
|source        | 입력 문자열                                    |필수            |
|조회        | 검색할 문자열                                  |필수            |
|start_index   | 검색 시작 위치                           |선택 사항            |
|length        | 검사할 문자 위치의 수입니다. -1은 무제한 길이를 정의 합니다. |선택 사항            |
|occurrence    | 패턴의 N 번째 모양에 대 한 인덱스를 찾습니다. 
                 기본값은 1, 첫 번째 항목의 인덱스입니다. |선택 사항            |

**반환**

*조회*의 인덱스 위치 (0부터 시작)입니다.

* 는 문자열을 입력에서 찾을 수 없으면-1을 반환 합니다.
* 다음과 같은 경우 *null* 을 반환 합니다.
     * start_index이 0 보다 작은 경우
     * 발생이 0 보다 작은 경우
     * 길이 매개 변수가-1 보다 작은 경우


**예**

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
