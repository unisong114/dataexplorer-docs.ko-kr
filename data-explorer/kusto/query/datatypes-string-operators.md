---
title: 문자열 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 문자열 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 845f0b5c9446f927fadf0141de4568cc28641c8d
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96320692"
---
# <a name="string-operators"></a>문자열 연산자

Kusto는 문자열 데이터 형식을 검색하기 위한 다양한 쿼리 연산자를 제공합니다. 이 문서에서는 문자열 용어가 인덱싱되는 방법을 설명하고, 문자열 쿼리 연산자를 나열하며, 성능을 최적화하기 위한 팁을 제공합니다.

## <a name="understanding-string-terms"></a>문자열 용어 이해

Kusto는 `string` 형식의 열을 포함하여 모든 열을 인덱싱합니다. 실제 데이터에 따라 이러한 열에 대해 여러 인덱스가 작성됩니다. 이러한 인덱스는 직접 공개되지 않지만, `has`, `!has`, `hasprefix`, `!hasprefix`와 같이 이름의 일부로 `has`가 포함된 `string` 연산자를 통해 쿼리에 사용됩니다. 이러한 연산자의 의미 체계는 열을 인코딩하는 방식에 따라 결정됩니다. 이러한 연산자는 "일반" 부분 문자열 일치를 수행하는 대신 *용어* 와 일치합니다.

### <a name="what-is-a-term"></a>용어란? 

기본적으로 각 `string` 값은 ASCII 영숫자의 최대 시퀀스로 분할되며, 각 시퀀스는 용어로 만들어집니다.
예를 들어 다음 `string`에서 용어는 `Kusto`, `WilliamGates3rd`이고 부분 문자열은 `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a`입니다.

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto는 *4자 이상* 의 모든 용어로 구성된 용어 인덱스를 작성하고, 이 인덱스는 `has`, `!has` 등에서 사용됩니다. 쿼리에서 4자 미만의 용어를 찾거나 `contains` 연산자를 사용하는 경우 Kusto에서 일치 항목을 확인할 수 없으면 열의 값을 검사하도록 되돌립니다. 이 방법은 용어 인덱스에서 용어를 조회하는 것보다 훨씬 느립니다.

## <a name="operators-on-strings"></a>문자열에 대한 연산자

> [!NOTE]
> 아래 표에는 다음 약어가 사용됩니다.
> * RHS = 식의 오른쪽
> * LHS = 식의 왼쪽
> 
> `_cs` 접미사가 있는 연산자는 대/소문자를 구분합니다.

연산자        |설명                                                       |대/소문자 구분|예제(`true` 생성)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |같음                                                            |예           |`"aBc" == "aBc"`
`!=`            |같지 않음                                                        |예           |`"abc" != "ABC"`
`=~`            |같음                                                            |아니요            |`"abc" =~ "ABC"`
`!~`            |같지 않음                                                        |아니요            |`"aBc" !~ "xyz"`
`has`           |오른쪽(RHS)이 왼쪽(LHS)의 전체 항임     |아니요            |`"North America" has "america"`
`!has`          |RHS가 LHS에서 전체 용어가 아님                                     |아니요            |`"North America" !has "amer"` 
[`has_any`](has-anyoperator.md)       |`has`와 동일하지만 모든 요소에서 작동함                    |아니요            |`"North America" has_any("south", "north")`
`has_cs`        |RHS가 LHS에서 전체 용어임                                        |예           |`"North America" has_cs "America"`
`!has_cs`       |RHS가 LHS에서 전체 용어가 아님                                     |예           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS가 LHS에서 용어 접두사임                                       |아니요            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS가 LHS에서 용어 접두사가 아님                                   |아니요            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS가 LHS에서 용어 접두사임                                       |예           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS가 LHS에서 용어 접두사가 아님                                   |예           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS가 LHS에서 용어 접미사임                                       |아니요            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS가 LHS에서 용어 접미사가 아님                                   |아니요            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS가 LHS에서 용어 접미사임                                       |예           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS가 LHS에서 용어 접미사가 아님                                   |예           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS가 LHS의 하위 시퀀스로 발생함                                |아니요            |`"FabriKam" contains "BRik"`
`!contains`     |RHS가 LHS에서 발생하지 않음                                         |아니요            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS가 LHS의 하위 시퀀스로 발생함                                |예           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS가 LHS에서 발생하지 않음                                         |예           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS가 LHS의 초기 하위 시퀀스임                              |아니요            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS가 LHS의 초기 하위 시퀀스가 아님                          |아니요            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS가 LHS의 초기 하위 시퀀스임                              |예           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS가 LHS의 초기 하위 시퀀스가 아님                          |예           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS가 LHS의 닫는 하위 시퀀스임                               |아니요            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS가 LHS의 닫는 하위 시퀀스가 아님                           |아니요            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS가 LHS의 닫는 하위 시퀀스임                               |예           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS가 LHS의 닫는 하위 시퀀스가 아님                           |예           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS가 RHS에 대한 일치 항목을 포함함                                      |예           |`"Fabrikam" matches regex "b.*k"`
[`in`](inoperator.md)            |요소 중 하나와 같음                                     |예           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |어떤 요소와도 같지 않음                                 |예           |`"bca" !in ("123", "345", "abc")`
`in~`           |요소 중 하나와 같음                                     |아니요            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |어떤 요소와도 같지 않음                                 |아니요            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> `has`가 포함된 모든 연산자는 부분 문자열 일치가 아니라 인덱싱된 4자 이상의 *용어* 에서 검색합니다. 용어는 문자열을 ASCII 영숫자의 시퀀스로 분할하여 만들어집니다. [문자열 용어 이해](#understanding-string-terms)를 참조하세요.

## <a name="performance-tips"></a>성능 팁

성능을 향상시키려면 동일한 작업을 수행하는 두 개의 연산자가 있는 경우 대/소문자를 구분하는 연산자를 사용합니다.
다음은 그 예입니다. 

* `=~` 대신 `==`를 사용합니다.
* `in~` 대신 `in`을 사용합니다.
* `contains` 대신 `contains_cs`를 사용합니다.

더 빠른 결과를 얻으려면 영숫자가 아닌 문자로 바인딩된 기호 또는 영숫자 단어가 있는지 또는 필드의 시작 또는 끝이 있는지 테스트하는 경우 `has` 또는 `in`을 사용합니다. 
`has`는 `contains`, `startswith` 또는 `endswith`보다 빠르게 작동합니다.

예를 들어 다음 쿼리 중 첫 번째 쿼리는 더 빠르게 실행됩니다.

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
