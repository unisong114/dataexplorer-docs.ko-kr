---
title: url_encode_component ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 url_encode_component ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 6740effd6a6117a2e63b5d03f09b38a723055f30
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241135"
---
# <a name="url_encode_component"></a>url_encode_component()

함수는 입력 URL의 문자를 인터넷을 통해 전송할 수 있는 형식으로 변환 합니다. 

URL 인코딩 및 디코딩에 대 한 자세한 내용은 [여기](https://en.wikipedia.org/wiki/Percent-encoding)에서 찾을 수 있습니다.
공백을 ' + '가 아닌 ' 20% '로 인코딩 [url_encode](./urlencodefunction.md) 와 다릅니다.

## <a name="syntax"></a>구문

`url_encode_component(`*url*`)`

## <a name="arguments"></a>인수

* *url*: 입력 url (문자열)입니다.  

## <a name="returns"></a>반환

인터넷을 통해 전송할 수 있는 형식으로 변환 된 URL (문자열)입니다.

## <a name="examples"></a>예

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|오리지널|인코딩된|
|---|---|
|https://www.bing.com/hello 단어씩|https %3 a %2 f %2 f www. bing .com% 2fhello% 20word|


 