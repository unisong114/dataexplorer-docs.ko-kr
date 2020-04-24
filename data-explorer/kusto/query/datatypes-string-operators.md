---
title: 문자열 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 문자열 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91955ef5877e6c054917f70c655fc4b7cd3f277b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516476"
---
# <a name="string-operators"></a>문자열 연산자

다음 표는 문자열에 대한 연산을 요약합니다.

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
`hasprefix`     |RHS는 LHS의 용어 접두사입니다.                                       |예            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS는 LHS의 용어 접두사가 아닙니다.                                   |예            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS는 LHS의 용어 접두사입니다.                                       |예           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS는 LHS의 용어 접두사가 아닙니다.                                   |예           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS는 LHS의 용어 접미사입니다.                                       |예            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS는 LHS에서 용어 접미사가 아닙니다.                                   |예            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS는 LHS의 용어 접미사입니다.                                       |예           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS는 LHS에서 용어 접미사가 아닙니다.                                   |예           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS가 LHS의 하위 시퀀스로 발생                                |예            |`"FabriKam" contains "BRik"`
`!contains`     |RHS가 LHS에서 발생하지 않음                                         |예            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS가 LHS의 하위 시퀀스로 발생                                |예           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS가 LHS에서 발생하지 않음                                         |예           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS가 LHS의 초기 하위 시퀀스임                              |예            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS는 LHS의 초기 하위 시퀀스가 아닙니다.                          |예            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS가 LHS의 초기 하위 시퀀스임                              |예           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS는 LHS의 초기 하위 시퀀스가 아닙니다.                          |예           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS가 LHS의 닫는 하위 시퀀스임                               |예            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS는 LHS의 닫는 하위 시퀀스가 아닙니다.                           |예            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS가 LHS의 닫는 하위 시퀀스임                               |예           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS는 LHS의 닫는 하위 시퀀스가 아닙니다.                           |예           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS가 RHS에 대한 일치 항목을 포함                                      |예           |`"Fabrikam" matches regex "b.*k"`
`in`            |요소 중 하나와 같음                                     |예           |`"abc" in ("123", "345", "abc")`
`!in`           |어떤 요소와도 같지 않음                                 |예           |`"bca" !in ("123", "345", "abc")`
`in~`           |요소 중 하나와 같음                                     |예            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |어떤 요소와도 같지 않음                                 |예            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |동일하지만 `has` 요소 중 에서 작동합니다.                    |예            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>성능 팁

성능 향상을 위해 동일한 작업을 수행하는 두 연산자가 있는 경우 대/소문자를 구분하는 연산자가 사용됩니다.
다음은 그 예입니다.

* `=~`을 사용합니다.`==`
* `in~`을 사용합니다.`in`
* `contains`을 사용합니다.`contains_cs`

더 빠른 결과를 얻으려면 영숫자가 아닌 문자(또는 필드의 시작 또는 끝)로 바인딩된 기호 또는 영숫자 단어가 `has` 있는지 `in`테스트하는 경우 또는 . `has``contains`보다 `startswith`빠르게 수행됩니다. `endswith`

예를 들어, 이러한 쿼리 중 첫 번째는 더 빠르게 실행됩니다.

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>문자열 용어 이해

기본적으로 Kusto는 형식의 `string`열을 포함하여 모든 열을 인덱싱합니다.
실제로 실제 데이터에 따라 이러한 열에 대해 여러 인덱스가 빌드됩니다. 사용자에게 이러한 인덱스는 `string` 이름: `has` `has`" , `!has` `hasprefix` `!hasprefix`" 등으로 있는 연산자를 제외하고는 쿼리 성능에 미치는 긍정적인 영향 이외에는 직접 노출되지 않습니다. 이러한 연산자는 열이 인코딩되는 방식에 따라 의미 체계가 지정된다는 점에서 특별합니다. "일반" 하위 문자열 일치를 수행하는 대신 이러한 연산자는 **용어와**일치합니다.

용어 기반 일치를 이해하려면 먼저 용어가 무엇인지 이해해야 합니다. 기본적으로 Kusto는 각 `string` 값을 ASCII 영구 문자의 최대 시퀀스로 나누고 각 값을 용어로 만들어집니다. 예를 들어 `string`다음의 용어는 `Kusto`" `WilliamGates3rd`및 다음 하위 문자열: `c1db` `4f9f` `88ef` `d94f3b6b0b5a` `ad67d136`" .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

기본적으로 Kusto는 4자 이상의 모든 용어로 구성된 용어 인덱스를 작성하고 이 인덱스는 `!has`4자 이상의 용어를 찾아볼 때 에 의해 `has`사용됩니다. 쿼리가 4자 보다 작은 용어를 찾거나 `contains` 연산자를 사용하는 경우 Kusto는 용어를 인덱스에서 위로 보는 것보다 훨씬 느린 일치를 확인할 수 없는 경우 열의 값을 검색으로 되돌립니다.

