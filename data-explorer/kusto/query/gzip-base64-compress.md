---
title: gzip_compress_to_base64_string-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 gzip_compress_to_base64_string () 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: b2bed8fab33de57da6286a7ea49b9378669a9d5a
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373853"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

Gzip 압축을 수행 하 고 결과를 b a s e 64로 인코딩합니다.


## <a name="syntax"></a>Syntax

`gzip_compress_to_base64_string("`*input_string* "`)`

## <a name="arguments"></a>인수

*input_string* : 입력 `string` , 압축 및 b a s e 64로 인코딩할 문자열입니다. 함수는 하나의 문자열 인수를 허용 합니다.

## <a name="returns"></a>반환

* Gzip 압축 및 b a s e `string` 64로 인코딩된 원래 문자열을 나타내는을 반환 합니다. 
* 압축 또는 인코딩이 실패 한 경우 빈 결과를 반환 합니다.

## <a name="example"></a>예제
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**출력:** 

| eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = |

## <a name="next-steps"></a>다음 단계

[Gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) 를 사용 하 여 원래 압축 되지 않은 문자열을 검색 합니다.
