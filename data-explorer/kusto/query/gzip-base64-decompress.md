---
title: gzip_decompress_from_base64_string ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 gzip_decompress_from_base64_string () 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148550"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ()

Base64에서 입력 문자열을 디코딩하고 gzip 압축 풀기를 수행 합니다.

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>인수

*input_string* : `string` gzip으로 압축 된 다음 b a s e 64로 인코딩된 입력입니다. 함수는 하나의 문자열 인수를 허용 합니다.

## <a name="returns"></a>반환

* 원래 문자열을 나타내는을 반환 합니다 `string` . 
* 압축 풀기 또는 디코딩에 실패 한 경우 빈 결과를 반환 합니다. 
    * 예를 들어 잘못 된 gzip 압축 및 64로 인코딩된 문자열은 빈 출력을 반환 합니다.

## <a name="examples"></a>예

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**출력:**

|" 1234567890qwertyuiop "|

잘못 된 입력의 예:

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**출력:**
>||

## <a name="next-steps"></a>다음 단계

[Gzip_compress_to_base64_string ()](gzip-base64-compress.md)를 사용 하 여 압축 된 입력 문자열을 만듭니다.
