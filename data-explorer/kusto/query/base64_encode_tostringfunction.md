---
title: base64_encode_tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_encode_tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 6fca71cbc7cf33f49a9f4698553391a84ddbb728
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252654"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

문자열을 base64 문자열로 인코딩합니다.

## <a name="syntax"></a>구문

`base64_encode_tostring(`*문자열*`)`

## <a name="arguments"></a>인수

* *문자열*: base64 문자열로 인코딩할 입력 문자열입니다.

## <a name="returns"></a>반환

Base64 문자열로 인코드된 문자열을 반환 합니다.

* Base64 문자열을 UTF-8 문자열로 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* Base64 문자열을 long 값 배열로 디코딩하려면 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 를 참조 하세요.


## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|방법   |
|--------|
|S3VzdG8 =|

