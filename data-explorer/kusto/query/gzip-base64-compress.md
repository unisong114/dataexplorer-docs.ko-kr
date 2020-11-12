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
ms.openlocfilehash: fffa39ca5fa41c065971b4feebfe60752b84ed59
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548872"
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

## <a name="example"></a>예
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**출력:** 

| H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA = = |

## <a name="next-steps"></a>다음 단계

* [Gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) 를 사용 하 여 원래 압축 되지 않은 문자열을 검색 합니다.
* 참고 항목 [zlib_compress_to_base64_string ()](zlib-base64-compress.md)
