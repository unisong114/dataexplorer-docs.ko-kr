---
title: indexof_regex ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 indexof_regex ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638974"
---
# <a name="indexof_regex"></a>indexof_regex()

입력 문자열에서 맨 처음 발견 되는 지정 된 조회 정규식의 인덱스 (0부터 시작)를 반환 합니다.

[`indexof()`](indexoffunction.md)을 참조하세요.

## <a name="syntax"></a>구문

`indexof_regex(`*원본* `,` *조회* `[,` *start_index* `[,` *길이* `[,` *발생*`]]])`

## <a name="arguments"></a>인수

|인수     | Description                                     |필수 또는 선택|
|--------------|-------------------------------------------------|--------------------|
|source        | 입력 문자열                                    |필수            |
|조회        | 정규식 조회 문자열입니다.               |필수            |
|start_index   | 검색 시작 위치                           |선택 사항            |
|length        | 검사할 문자 위치의 수입니다. -1은 무제한 길이를 정의 합니다. |선택 사항            |
|occurrence    | 패턴의 N 번째 모양에 대 한 인덱스를 찾습니다. 
                 기본값은 1, 첫 번째 항목의 인덱스입니다. |선택 사항            |

## <a name="returns"></a>반환

*조회* 의 인덱스 위치 (0부터 시작)입니다.

* 는 문자열을 입력에서 찾을 수 없으면-1을 반환 합니다.
* 다음과 같은 경우 *null* 을 반환 합니다.
     * start_index이 0 보다 작은 경우
     * 발생이 0 보다 작은 경우
     * 길이 매개 변수가-1 보다 작은 경우

> [!NOTE]
- 겹치는 일치 조회는 지원 되지 않습니다.
- 정규식 문자열에는 이스케이프 하거나 @ ' ' 문자열-리터럴을 사용 해야 하는 문자가 포함 될 수 있습니다.

## <a name="examples"></a>예

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
