---
title: base64_encode_fromarray() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 base64_encode_fromarray()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518074"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

바이트 배열에서 base64 문자열을 인코딩합니다.

**구문**

`base64_encode_fromarray(`*바이트 배열*`)`

**인수**

* *BytesArray*: base64 문자열로 인코딩할 입력 바이트 배열입니다.

**반환**

바이트 배열에서 인코딩된 base64 문자열을 반환합니다.

* UTF-8 문자열에 base64 문자열을 디코딩하는 경우 [base64_decode_tostring()](base64_decode_tostringfunction.md)
* base64 문자열에 대한 인코딩 문자열의 경우 [base64_encode_tostring()](base64_encode_tostringfunction.md)
* 이 함수는 [base64_decode_toarray()](base64_decode_toarrayfunction.md)

**예제**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8=|


잘못된 UTF-8 인코딩된 문자열에서 생성된 잘못된 바이트 배열에서 base64 문자열을 인코딩하려고 하면 null이 반환됩니다.

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||