---
title: trim ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 trim ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a28ca267612bef68c676118331b3010a8c947e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350641"
---
# <a name="trim"></a>trim()

지정 된 정규식에 대 한 선행 및 후행 일치 항목을 모두 제거 합니다.

## <a name="syntax"></a>구문

`trim(`*regex* `,` *텍스트*`)`

## <a name="arguments"></a>인수

* *regex*: 문자열 또는 *텍스트*의 끝 부분에서 잘라내는 [정규식](re2.md) 입니다.  
* *text*: 문자열입니다.

## <a name="returns"></a>반환

*텍스트*의 시작 부분 또는 끝 부분에 있는 *regex* 의 일치 항목을 지운 후의 *텍스트* 입니다.

## <a name="example"></a>예제

문이 아래 *string_to_trim*의 시작 부분 및 끝 *부분에서 부분 문자열* 을 자릅니다.

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

다음 문은 문자열의 시작과 끝에서 모든 비단어 문자를 자릅니다.

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te st1//$|Te st1|
|-Te st2//$|Te st2|
|-Te st3//$|Te st3|
|-Te st4//$|Te st4|
|-Te st5//$|Te st5|


 