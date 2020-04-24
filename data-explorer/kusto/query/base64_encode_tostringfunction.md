---
title: base64_encode_tostring() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 base64_encode_tostring()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518057"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

문자열을 base64 문자열로 인코딩합니다.

**구문**

`base64_encode_tostring(`*문자열*`)`

**인수**

* *문자열*: base64 문자열로 인코딩할 입력 문자열입니다.

**반환**

base64 문자열로 인코딩된 문자열을 반환합니다.

* UTF-8 문자열에 base64 문자열을 디코딩하는 경우 [base64_decode_tostring()](base64_decode_tostringfunction.md)
* 긴 값의 배열에 base64 문자열을 디코딩하는 경우 [base64_decode_toarray()](base64_decode_toarrayfunction.md)


**예제**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|퀸 (퀸즈)(예: 퀸   |
|--------|
|S3VzdG8=|
