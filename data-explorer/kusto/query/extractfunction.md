---
title: 추출() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 추출()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 398c79ddcd362f3c09fea18accd082c0eb3b1fc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515371"
---
# <a name="extract"></a>extract()

텍스트 문자열에서 [정규식](./re2.md) 에 대한 일치 항목을 가져옵니다. 

선택적으로 추출된 하위 문자열을 표시된 유형으로 변환합니다.

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

**구문**

`extract(`*정규식* `,` *캡처그룹* `,` `,` *텍스트* [ 문자 *Literal*]`)`

**인수**

* *정규식*: [정규식](./re2.md).
* *캡처 그룹*: `int` 추출할 캡처 그룹을 나타내는 양수 상수입니다. 0은 전체 일치, 1은 정규식의 첫 번째 '(' 괄호')'에 의해 일치된 값, 2 이상은 이후 괄호를 나타냅니다.
* *텍스트*: `string` 검색할 수 있습니다.
* *typeLiteral*: 선택적 문자 리터럴(예: `typeof(long)`)입니다. 제공된 경우 추출된 부분 문자열이 이 형식으로 변환됩니다. 

**반환**

*regex*가 *text*에서 일치 항목을 찾은 경우: 지시된 캡처 그룹 *captureGroup*에 대해 일치된 부분 문자열, 선택적으로 *typeLiteral*로 변환됩니다.

일치 항목이 없거나 형식 변환에 실패한 경우 `null`입니다. 

**예**

예제 문자열 `Trace`에서 `Duration`에 대한 정의를 검색합니다. 일치 항목은 `real`로 변환된 다음, 시간 상수(`1s`)를 곱하여 `Duration`이 `timespan` 형식이 됩니다. 이 예제에서 이는 123.45초와 같음:

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

이 예제는 `substring(Text, 2, 4)`과 같습니다.

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```