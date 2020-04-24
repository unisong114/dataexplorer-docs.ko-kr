---
title: 대체() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 replace()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84a741f10172ef418da7d92b8c1ad6ba26593d72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510339"
---
# <a name="replace"></a>replace()

모든 정규식 일치 항목을 다른 문자열로 바꿉니다.

**구문**

`replace(`*정규식* `,` *다시 쓰기* `,` *텍스트*`)`

**인수**

* *정규식*: *텍스트를*검색하는 [정규식](https://github.com/google/re2/wiki/Syntax) . '('괄호')'에 캡처 그룹을 포함할 수 있습니다. 
* *다시 쓰기*: 일치에 의해 만들어진 모든 일치에 대한 대체 정규식Regex . *matchingRegex* 전체 일치를 참조하려면 `\0`, 첫 번째 캡처 그룹을 참조하려면 `\1`, 이후 캡처 그룹을 참조하려면 `\2`를 사용하는 식입니다.
* *텍스트*: 문자열입니다.

**반환**

*regex*의 모든 일치 항목을 *rewrite*로 바꾼 후의 *text*입니다. 일치 항목은 겹치지 않습니다.

**예제**

이 문은:

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

다음과 같은 결과를 나타냄:

| x    | str | 대체됨|
|---|---|---|
| 1    | 숫자는 1.000000임  | 이전 숫자: 1.000000|
| 2    | 숫자는 2.000000임  | 이전 숫자: 2.000000|
| 3    | 숫자는 3.000000임  | 이전 숫자: 3.000000|
| 4    | 숫자는 4.000000임  | 이전 숫자: 4.000000|
| 5    | 숫자는 5.000000임  | 이전 숫자: 5.000000|
 