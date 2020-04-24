---
title: trim_start() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 trim_start()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505579"
---
# <a name="trim_start"></a>trim_start()

지정된 정규식의 선행 일치를 제거합니다.

**구문**

`trim_start(`*정규식* `,` *텍스트*`)`

**인수**

* *정규식*: 문자열 또는 [정규식은](re2.md) *텍스트의*시작 부분에서 잘라.  
* *텍스트*: 문자열입니다.

**반환**

*텍스트의* 시작 부분에 있는 *정규식의* 일치를 트리밍 한 후 *텍스트*.

**예제**

문 벨로우는 *string_to_trim*시작부터 *하위 문자열을* 트림합니다.

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

다음 문은 문자열의 시작 부분에서 모든 비 단어 문자를 잘라내입니다.

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- 테 st1 / / $|테 st1 // $|
|- 테 st2 / / $|테 st2 // $|
|- Te st3 / / $|테 st3 // $|
|- Te st4// $|테 st4 // $|
|- Te st5 / / $|테 st5 // $|

 