---
title: url_decode ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 url_decode ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251859"
---
# <a name="url_decode"></a>url_decode()

함수는 인코딩된 URL을 일반 URL 표현으로 변환 합니다. 

URL 디코딩 및 인코딩에 대 한 자세한 내용은 [여기](https://en.wikipedia.org/wiki/Percent-encoding)에서 찾을 수 있습니다.

## <a name="syntax"></a>구문

`url_decode(`*인코딩된 url*`)`

## <a name="arguments"></a>인수

* *인코딩된 url*: 인코드된 url (문자열)입니다.  

## <a name="returns"></a>반환

일반 표현의 URL (문자열)입니다.

## <a name="examples"></a>예

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|오리지널|디코딩해야|
|---|---|
|https %3 a %2 f %2 f www. bing% 2f.|https://www.bing.com/|



 