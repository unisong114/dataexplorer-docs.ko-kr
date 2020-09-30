---
title: zlib_compress_to_base64_string-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 zlib_compress_to_base64_string () 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573539"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ()

Zlib 압축을 수행 하 고 결과를 b a s e 64로 인코딩합니다.

> [!NOTE]
> 유일 하 게 지원 되는 windows 크기는 15입니다.

## <a name="syntax"></a>구문

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>인수

*input_string*: 입력 `string` , 압축 및 b a s e 64로 인코딩할 문자열입니다. 함수는 하나의 문자열 인수를 허용 합니다.

## <a name="returns"></a>반환

* Zlib 압축 및 b a s e `string` 64로 인코딩된 원래 문자열을 나타내는을 반환 합니다. 
* 압축 또는 인코딩이 실패 한 경우 빈 결과를 반환 합니다.

## <a name="example"></a>예제

### <a name="using-kusto"></a>Kusto 사용

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**출력:** | " eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>Python 사용

다른 도구 (예: Python)를 사용 하 여 압축을 수행할 수 있습니다. 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>다음 단계

[Zlib_decompress_from_base64_string ()](zlib-base64-decompress.md) 를 사용 하 여 원래 압축 되지 않은 문자열을 검색 합니다.