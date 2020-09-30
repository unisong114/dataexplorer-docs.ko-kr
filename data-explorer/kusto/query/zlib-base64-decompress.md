---
title: zlib_decompress_from_base64_string ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 zlib_decompress_from_base64_string () 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573528"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ()

Base64에서 입력 문자열을 디코딩하고 zlib 압축 풀기를 수행 합니다.

> [!NOTE]
> 유일 하 게 지원 되는 windows 크기는 15입니다.

## <a name="syntax"></a>구문

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>인수

*input_string*: zlib로 압축 된 후 b a s e `string` 64로 인코딩된 입력입니다. 함수는 하나의 문자열 인수를 허용 합니다.

## <a name="returns"></a>반환

* 원래 문자열을 나타내는을 반환 합니다 `string` . 
* 압축 풀기 또는 디코딩에 실패 한 경우 빈 결과를 반환 합니다. 
    * 예를 들어 잘못 된 zlib 압축 및 64로 인코딩된 문자열은 빈 출력을 반환 합니다.

## <a name="examples"></a>예제

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**출력:**

| 테스트 문자열 123 |

잘못 된 입력의 예:

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**출력**
||

## <a name="next-steps"></a>다음 단계

[Zlib_compress_to_base64_string ()](zlib-base64-compress.md)를 사용 하 여 압축 된 입력 문자열을 만듭니다.