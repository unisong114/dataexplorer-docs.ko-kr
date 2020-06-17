---
title: base64_decode_tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_decode_tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: ffef2fd609075b0d5e5af5c4064e079c27cd8c94
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818496"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Base64 문자열을 UTF-8 문자열로 디코딩합니다.

**구문**

`base64_decode_tostring(`*문자열*`)`

**인수**

* *문자열*: BASE64에서 UTF8-8 문자열로 디코딩할 입력 문자열입니다.

**반환**

Base64 문자열에서 디코딩된 UTF-8 문자열을 반환 합니다.

* Base64 문자열을 long 값 배열로 디코딩하려면 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 를 참조 하세요.
* 문자열을 base64 문자열로 디코딩하려면 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 를 참조 하세요.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|방법|
|-----|
|Kusto|

잘못 된 UTF-8 인코딩으로 생성 된 base64 문자열을 디코딩하 려 면 null이 반환 됩니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
