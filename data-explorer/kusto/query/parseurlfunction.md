---
title: parse_url() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_url()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511495"
---
# <a name="parse_url"></a>parse_url()

절대 URL을 `string` 구문 분석 `dynamic` 하 고 URL 부분이 포함 된 개체를 반환 합니다.


**구문**

`parse_url(`*Url*`)`

**인수**

* *URL*: 문자열은 URL또는 URL의 쿼리 부분을 나타냅니다.

**반환**

URL 구성 요소를 포함 [하는](./scalar-data-types/dynamic.md) 동적 형식의 개체: 구성표, 호스트, 포트, 경로, 사용자 이름, 암호, 쿼리 매개 변수, 조각.

**예제**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

결과가 나타납니다.

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```