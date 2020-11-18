---
title: 문자열 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 문자열 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.openlocfilehash: 1c394d9125bd354cab21d3087c273f983627a0d6
ms.sourcegitcommit: c351c2c8ab6e184827c4702eb0ec8bf783c7bbd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874783"
---
# <a name="string-operators"></a>문자열 연산자

Kusto는 문자열 데이터 형식을 검색 하기 위한 다양 한 쿼리 연산자를 제공 합니다. 다음 문서에서는 문자열 용어를 인덱싱하는 방법에 대해 설명 하 고, 문자열 쿼리 연산자를 나열 하 고, 성능을 최적화 하기 위한 팁을 제공 합니다.

## <a name="understanding-string-terms"></a>문자열 용어 이해

Kusto는 형식의 열을 포함 하 여 모든 열을 인덱싱합니다 `string` . 실제 데이터에 따라 이러한 열에 대해 여러 인덱스를 작성 합니다. 이러한 인덱스는 직접 노출 되지 않지만,,, 등의 이름에 포함 된 연산자를 사용 하 여 쿼리에 사용 됩니다 `string` `has` `has` `!has` `hasprefix` `!hasprefix` . 이러한 연산자의 의미 체계는 열이 인코딩 되는 방식에 따라 결정 됩니다. "일반" 부분 문자열 일치를 수행 하는 대신 이러한 연산자는 *용어* 와 일치 합니다.

### <a name="what-is-a-term"></a>용어 란 무엇 인가요? 

기본적으로 각 `string` 값은 ASCII 영숫자 문자의 최대 시퀀스로 분할 되 고 이러한 각 시퀀스는 용어로 생성 됩니다.
예를 들어, 다음과 같은 용어는,,,, 등의 `string` `Kusto` `WilliamGates3rd` 부분 문자열 `ad67d136` `c1db` `4f9f` `88ef` `d94f3b6b0b5a` 입니다.

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto는 *4 자 이상인* 모든 용어로 구성 된 용어 인덱스를 작성 하 고, 등에서이 인덱스를 사용 `has` `!has` 합니다. 쿼리가 4 자 보다 작은 용어를 찾거나 연산자를 사용 하는 경우 `contains` Kusto는 일치 항목을 확인할 수 없는 경우 열에 있는 값을 검색 하도록 되돌립니다. 이 방법은 용어 인덱스에서 용어를 찾는 것 보다 훨씬 느립니다.

## <a name="operators-on-strings"></a>문자열에 대 한 연산자

> [!NOTE]
> 아래 표에는 다음 약어가 사용 됩니다.
> * RHS = 식의 오른쪽
> * LHS = 식의 왼쪽
> 
> 접미사가 있는 연산자 `_cs` 는 대/소문자를 구분 합니다.

연산자        |Description                                                       |대/소문자 구분|예제(`true` 생성)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |같음                                                            |예           |`"aBc" == "aBc"`
`!=`            |같지 않음                                                        |예           |`"abc" != "ABC"`
`=~`            |같음                                                            |예            |`"abc" =~ "ABC"`
`!~`            |같지 않음                                                        |예            |`"aBc" !~ "xyz"`
`has`           |오른쪽(RHS)이 왼쪽(LHS)의 전체 항임     |예            |`"North America" has "america"`
`!has`          |RHS는 LHS에서 전체 용어가 아닙니다.                                     |예            |`"North America" !has "amer"` 
`has_cs`        |RHS는 LHS의 전체 용어입니다.                                        |예           |`"North America" has_cs "America"`
`!has_cs`       |RHS는 LHS에서 전체 용어가 아닙니다.                                     |예           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS는 LHS에서 용어 접두사입니다.                                       |예            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS는 LHS에서 용어 접두사가 아닙니다.                                   |예            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS는 LHS에서 용어 접두사입니다.                                       |예           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS는 LHS에서 용어 접두사가 아닙니다.                                   |예           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS는 LHS에서 용어 접미사입니다.                                       |예            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS는 LHS에서 용어 접미사가 아닙니다.                                   |예            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS는 LHS에서 용어 접미사입니다.                                       |예           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS는 LHS에서 용어 접미사가 아닙니다.                                   |예           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS가 LHS의 하위 시퀀스로 발생함                                |예            |`"FabriKam" contains "BRik"`
`!contains`     |RHS가 LHS에서 발생하지 않음                                         |예            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS가 LHS의 하위 시퀀스로 발생함                                |예           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS가 LHS에서 발생하지 않음                                         |예           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS가 LHS의 초기 하위 시퀀스임                              |예            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS가 LHS의 초기 하위 시퀀스가 아님                          |예            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS가 LHS의 초기 하위 시퀀스임                              |예           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS가 LHS의 초기 하위 시퀀스가 아님                          |예           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS가 LHS의 닫는 하위 시퀀스임                               |예            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS가 LHS의 닫는 하위 시퀀스가 아님                           |예            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS가 LHS의 닫는 하위 시퀀스임                               |예           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS가 LHS의 닫는 하위 시퀀스가 아님                           |예           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS가 RHS에 대한 일치 항목을 포함함                                      |예           |`"Fabrikam" matches regex "b.*k"`
`in`            |요소 중 하나와 같음                                     |예           |`"abc" in ("123", "345", "abc")`
`!in`           |어떤 요소와도 같지 않음                                 |예           |`"bca" !in ("123", "345", "abc")`
`in~`           |요소 중 하나와 같음                                     |예            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |어떤 요소와도 같지 않음                                 |예            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |와 동일 `has` 하지만 모든 요소에서 작동 합니다.                    |예            |`"North America" has_any("south", "north")`

> [!TIP]
> 네 개 이상의 `has` 문자로 이루어진 인덱싱된 *용어* 에 대 한 검색을 포함 하는 모든 연산자와 부분 문자열이 일치 합니다. 문자열을 ASCII 영숫자의 시퀀스로 분리 하 여 용어를 만듭니다. [문자열 용어 이해](#understanding-string-terms)를 참조 하세요.

## <a name="performance-tips"></a>성능 팁

동일한 태스크를 수행 하는 두 개의 연산자가 있는 경우에는 대/소문자를 구분 하는 연산자를 사용 하 여 성능을 향상 시킬 수 있습니다.
다음은 그 예입니다. 

* 대신 `=~` 를 사용 합니다. `==`
* 대신 `in~` 를 사용 합니다. `in`
* 대신 `contains` 를 사용 합니다. `contains_cs`

더 빠른 결과를 위해 영숫자가 아닌 문자 또는 필드의 시작 또는 끝에 의해 바인딩된 기호 또는 영숫자 단어가 있는지 테스트 하는 경우 또는를 사용 `has` `in` 합니다. 
`has``contains`는, 또는 보다 빠르게 작동 `startswith` `endswith` 합니다.

예를 들어 이러한 쿼리 중 첫 번째 쿼리는 더 빠르게 실행 됩니다.

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
