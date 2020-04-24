---
title: url_encode() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 url_encode()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 913be2d20af413f8ba89192f4db57e60fc6d7b27
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505052"
---
# <a name="url_encode"></a>url_encode()

이 함수는 입력 URL의 문자를 인터넷을 통해 전송할 수 있는 형식으로 변환합니다. 

URL 인코딩 및 디코딩에 대한 자세한 내용은 [여기에서](https://en.wikipedia.org/wiki/Percent-encoding)확인할 수 있습니다.
공백을 '+'가 아닌 '+'로 인코딩하여 [url_encode_component](./urlencodecomponentfunction.md) 다릅니다(여기서 는 응용 프로그램/x-www-form-urlencoded 참조). [here](https://en.wikipedia.org/wiki/Percent-encoding)

**구문**

`url_encode(`*Url*`)`

**인수**

* *URL*: 입력 URL (문자열).  

**반환**

URL(문자열)은 인터넷을 통해 전송할 수 있는 형식으로 변환됩니다.

**예**

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|오리지널|인코딩된|
|---|---|
|https://www.bing.com/hello단어 /|https%3a%2f%2fwww.bing.com%2fhello+단어|


 