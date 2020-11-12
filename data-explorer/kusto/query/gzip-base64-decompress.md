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
ms.openlocfilehash: a35fd4ed2f43c991aa08e2e1594103cb5f5bd7a7
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548889"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

Base64에서 입력 문자열을 디코딩하고 gzip 압축 풀기를 수행 합니다.

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>인수

*input_string* : `string` gzip으로 압축 된 다음 b a s e 64로 인코딩된 입력입니다. 함수는 하나의 문자열 인수를 허용 합니다.

> [!NOTE]
> 이 함수는 필수 gzip 헤더 필드 (ID1, ID2 및 CM)를 확인 하 고, 이러한 필드에 잘못 된 값이 있는 경우 빈 출력을 반환 합니다.
> 선택적 헤더 필드는 지원 되지 않습니다. FLG와 XFL 모두 0으로 예상 됩니다.


## <a name="returns"></a>반환

* 원래 문자열을 나타내는을 반환 합니다 `string` . 
* 압축 풀기 또는 디코딩에 실패 한 경우 빈 결과를 반환 합니다. 
    * 예를 들어 잘못 된 gzip 압축 및 64로 인코딩된 문자열은 빈 출력을 반환 합니다.

## <a name="examples"></a>예제

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
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

* [Gzip_compress_to_base64_string ()](gzip-base64-compress.md)를 사용 하 여 압축 된 입력 문자열을 만듭니다.
* [Zlib_decompress_from_base64_string](zlib-base64-decompress.md)도 참조 하세요.
