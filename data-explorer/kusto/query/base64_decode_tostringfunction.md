---
title: base64_decode_tostring() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 base64_decode_tostring()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518176"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

기본 64 문자열을 UTF-8 문자열로 디코딩

**구문**

`base64_decode_tostring(`*문자열*`)`

**인수**

* *문자열*: base64에서 UTF8-8 문자열로 디코딩할 입력 문자열입니다.

**반환**

base64 문자열에서 디코딩된 UTF-8 문자열을 반환합니다.

* 긴 값의 배열에 base64 문자열을 디코딩하는 경우 [base64_decode_toarray()](base64_decode_toarrayfunction.md)
* base64 문자열에 대한 인코딩 문자열의 경우 [base64_encode_tostring()](base64_encode_tostringfunction.md)

**예제**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|퀸 (퀸즈)(예: 퀸|
|-----|
|Kusto|

잘못된 UTF-8 인코딩에서 생성된 base64 문자열을 디코딩하려고 하면 null이 반환됩니다.

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||