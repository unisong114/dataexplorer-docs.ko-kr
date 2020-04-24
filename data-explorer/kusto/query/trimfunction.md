---
title: 트림() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 trim()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505596"
---
# <a name="trim"></a>trim()

지정된 정규식의 모든 선행 및 후행 일치 항목을 제거합니다.

**구문**

`trim(`*정규식* `,` *텍스트*`)`

**인수**

* *정규식*: 문자열 또는 [정규식은](re2.md) *텍스트의*시작 및 / 또는 끝에서 잘라 .  
* *텍스트*: 문자열입니다.

**반환**

*텍스트의* 시작 및/또는 끝에 있는 *정규식의* 일치 항목을 트리밍한 후 *텍스트*.

**예제**

문 벨로우는 *string_to_trim*시작과 끝에서 *하위 문자열을* 트림합니다.

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Next 문은 문자열의 시작과 끝에서 모든 비단어 문자를 잘라내며 다음과 같습니다.

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- 테 st1 / / $|테 st1|
|- 테 st2 / / $|테 st2|
|- Te st3 / / $|테 st3|
|- Te st4// $|테 st4|
|- Te st5 / / $|테 st5|


 