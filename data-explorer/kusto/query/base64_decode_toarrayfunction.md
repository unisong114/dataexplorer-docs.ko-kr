---
title: base64_decode_toarray() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 base64_decode_toarray()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 80a702f112fd4d7b88b4011b3f615cf34acff62c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518193"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

base64 문자열을 긴 값의 배열로 디코딩합니다.

**구문**

`base64_decode_toarray(`*문자열*`)`

**인수**

* *문자열*: base64에서 UTF8-8 문자열로 디코딩할 입력 문자열입니다.

**반환**

base64 문자열에서 이코딩된 긴 값의 배열을 반환합니다.

* UTF-8 문자열에 base64 문자열을 디코딩하는 경우 [base64_decode_tostring()](base64_decode_tostringfunction.md)
* base64 문자열에 대한 인코딩 문자열의 경우 [base64_encode_tostring()](base64_encode_tostringfunction.md)

**예제**

```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|퀸 (퀸즈)(예: 퀸|
|-----|
|[75,117,115,116,111]|

잘못된 UTF-8 인코딩에서 생성된 base64 문자열을 디코딩하려고 하면 null이 반환됩니다.

```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||