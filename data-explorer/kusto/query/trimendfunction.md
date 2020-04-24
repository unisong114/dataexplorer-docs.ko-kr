---
title: trim_end() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 trim_end()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505647"
---
# <a name="trim_end"></a>trim_end()

지정된 정규식의 후행 일치를 제거합니다.

**구문**

`trim_end(`*정규식* `,` *텍스트*`)`

**인수**

* *정규식*: 문자열 또는 [정규식은](re2.md) *텍스트의*끝에서 잘라.  
* *텍스트*: 문자열입니다.

**반환**

*텍스트* 끝에 있는 *정규식의* 일치 항목을 트리밍한 후 *텍스트입니다.*

**예제**

문 벨로우는 *string_to_trim*끝에서 *하위 문자열을* 트림 :

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |빙          |

다음 문은 문자열의 끝에서 모든 비 단어 문자를 잘라내입니다.

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|- 테 st1 / / $|- 테 st1  |
|- 테 st2 / / $|- Te st2  |
|- Te st3 / / $|- Te st3  |
|- Te st4// $|- Te st4  |
|- Te st5 / / $|- Te st5  |