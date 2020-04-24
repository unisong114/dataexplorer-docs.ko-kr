---
title: url_decode() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 url_decode()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505154"
---
# <a name="url_decode"></a>url_decode()

이 함수는 인코딩된 URL을 일반 URL 표현으로 변환합니다. 

URL 디코딩 및 인코딩에 대한 자세한 내용은 [여기에서](https://en.wikipedia.org/wiki/Percent-encoding)확인할 수 있습니다.

**구문**

`url_decode(`*인코딩된 URL*`)`

**인수**

* *인코딩된 URL*: 인코딩된 URL(문자열)을 참조하십시오.  

**반환**

일반 표현의 URL(문자열)입니다.

**예**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|오리지널|디코딩된|
|---|---|
|https%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 