---
title: 문자열 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 문자열 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e16d90c8307392536b5971758ce7df15a9ea1b46
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977136"
---
# <a name="string-operators"></a>문자열 연산자

다음 표에서는 문자열에 대 한 연산자를 요약 합니다.

연산자        |Description                                                       |대/소문자 구분|예제(`true` 생성)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |같음                                                            |예           |`"aBc" == "aBc"`
`!=`            |같지 않음                                                        |예           |`"abc" != "ABC"`
`=~`            |같음                                                            |예            |`"abc" =~ "ABC"`
`!~`            |같지 않음                                                        |예            |`"aBc" !~ "xyz"`
`has`           |오른쪽(RHS)이 왼쪽(LHS)의 전체 항임     |예            |`"North America" has "america"`
`!has`          |RHS가 LHS의 전체 항이 아님                                     |예            |`"North America" !has "amer"` 
`has_cs`        |오른쪽(RHS)이 왼쪽(LHS)의 전체 항임     |예           |`"North America" has_cs "America"`
`!has_cs`       |RHS가 LHS의 전체 항이 아님                                     |예           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS는 LHS에서 용어 접두사입니다.                                       |예            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS는 LHS에서 용어 접두사가 아닙니다.                                   |예            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS는 LHS에서 용어 접두사입니다.                                       |예           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS는 LHS에서 용어 접두사가 아닙니다.                                   |예           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS는 LHS에서 용어 접미사입니다.                                       |예            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS가 LHS에서 용어 접미사가 아닙니다.                                   |예            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS는 LHS에서 용어 접미사입니다.                                       |예           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS가 LHS에서 용어 접미사가 아닙니다.                                   |예           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS가 LHS의 하위 시퀀스로 발생                                |예            |`"FabriKam" contains "BRik"`
`!contains`     |RHS가 LHS에서 발생하지 않음                                         |예            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS가 LHS의 하위 시퀀스로 발생                                |예           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS가 LHS에서 발생하지 않음                                         |예           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS가 LHS의 초기 하위 시퀀스임                              |예            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS가 LHS의 초기 하위 시퀀스 아닙니다.                          |예            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS가 LHS의 초기 하위 시퀀스임                              |예           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS가 LHS의 초기 하위 시퀀스 아닙니다.                          |예           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS가 LHS의 닫는 하위 시퀀스임                               |예            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS가 LHS의 닫는 하위 시퀀스가 아닙니다.                           |예            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS가 LHS의 닫는 하위 시퀀스임                               |예           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS가 LHS의 닫는 하위 시퀀스가 아닙니다.                           |예           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS가 RHS에 대한 일치 항목을 포함                                      |예           |`"Fabrikam" matches regex "b.*k"`
`in`            |요소 중 하나와 같음                                     |예           |`"abc" in ("123", "345", "abc")`
`!in`           |어떤 요소와도 같지 않음                                 |예           |`"bca" !in ("123", "345", "abc")`
`in~`           |요소 중 하나와 같음                                     |예            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |어떤 요소와도 같지 않음                                 |예            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |와 `has` 동일 하지만 모든 요소에서 작동 합니다.                    |예            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>성능 팁

성능을 향상 시키려면 동일한 태스크를 수행 하는 두 개의 연산자가 있는 경우 대/소문자를 구분 하는 연산자를 사용 합니다.
다음은 그 예입니다. 

* 대신 `=~`를 사용 합니다.`==`
* 대신 `in~`를 사용 합니다.`in`
* 대신 `contains`를 사용 합니다.`contains_cs`

더 빠른 결과를 위해 영숫자가 아닌 문자 (또는 필드의 시작 또는 끝)로 바인딩되는 기호 또는 영숫자 단어가 있는지 테스트 하는 경우 또는 `has` `in`를 사용 합니다. `has`는, `contains` `startswith`또는 `endswith`보다 더 빠르게 수행 됩니다.

예를 들어 이러한 쿼리 중 첫 번째 쿼리는 더 빠르게 실행 됩니다.

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>문자열 용어 이해

기본적으로 Kusto는 형식의 `string`열을 포함 하 여 모든 열을 인덱싱합니다.
실제로는 실제 데이터에 따라 이러한 열에 대해 여러 인덱스가 작성 됩니다. 사용자에 게는 `string` `has` `has`, `!has`, `hasprefix`, `!hasprefix`등의 이름을 포함 하는 연산자를 제외 하 고, 이러한 인덱스는 직접 노출 되지 않습니다 (물론 쿼리 성능에 대 한 긍정적인 영향을 주지 않음). 이러한 연산자는 열이 인코딩 되는 방식에 따라 의미 체계를 결정 한다는 특수 한 연산자입니다. "일반" 부분 문자열 일치를 수행 하는 대신 이러한 연산자는 **용어**와 일치 합니다.

용어 기반 일치를 이해 하려면 먼저 용어를 이해 해야 합니다. 기본적으로 Kusto는 각 `string` 값을 ASCII 영숫자 문자의 최대 시퀀스로 나누고 각 값은 용어로 생성 됩니다. 예 `string`를 들어, 다음과 같은 용어는, 및 `Kusto`이며 `WilliamGates3rd`, `ad67d136` `c1db` `4f9f`,, `88ef`, `d94f3b6b0b5a`등의 하위 문자열입니다.

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

기본적으로 kusto는 **4 자 이상**으로 구성 된 모든 용어로 구성 된 용어 인덱스를 작성 하며, `has` `!has`, 등에서 4 자 이상의 용어를 조회할 때이 인덱스를 사용 합니다. (쿼리가 4 자 보다 작은 용어를 찾거나 연산자를 `contains` 사용 하는 경우, 예를 들어, Kusto는 일치 항목을 확인할 수 없는 경우 열에 있는 값을 검색 하기 위해를 되돌립니다 .이는 용어 인덱스에서 용어를 찾는 것 보다 훨씬 느립니다.)

