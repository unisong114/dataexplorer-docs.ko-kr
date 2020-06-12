---
title: base64_decode_toarray ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_decode_toarray ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: df10ea2bbdf4e48d32c087d35eb9361fc4d697b8
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717209"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Base64 문자열을 long 값 배열로 디코딩합니다.

**구문**

`base64_decode_toarray(`*문자열*`)`

**인수**

* *문자열*: BASE64에서 UTF8 문자열로 디코딩할 입력 문자열입니다.

**반환**

Base64 문자열에서 디코딩된 long 값의 배열을 반환 합니다.

* Base64 문자열을 UTF-8 문자열로 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* 문자열을 base64 문자열로 인코딩하려면 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 를 참조 하세요.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|방법|
|-----|
|[75117115116111]|

잘못 된 UTF-8 인코딩에서 생성 된 base64 문자열을 디코딩하 려 면 "null"이 반환 됩니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
