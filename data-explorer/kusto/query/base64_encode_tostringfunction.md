---
title: base64_encode_tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_encode_tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 332ff6bedd268dd79be020ff1dc4d0591ed486f7
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225312"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

문자열을 base64 문자열로 인코딩합니다.

**구문**

`base64_encode_tostring(`*문자열*`)`

**인수**

* *문자열*: base64 문자열로 인코딩할 입력 문자열입니다.

**반환**

Base64 문자열로 인코드된 문자열을 반환 합니다.

* UTF-8 문자열로 base64 문자열을 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* Base64 문자열을 long 값 배열로 디코딩하려면 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 를 참조 하세요.


**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|방법   |
|--------|
|S3VzdG8 =|
