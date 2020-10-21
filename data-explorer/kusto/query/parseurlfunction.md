---
title: parse_url ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_url ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ce68151270369bd7dc0898b98029bacddf0c243
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248627"
---
# <a name="parse_url"></a>parse_url()

절대 URL을 구문 분석 `string` 하 고 url 부분을 포함 하는 개체를 반환 합니다 `dynamic` .


## <a name="syntax"></a>구문

`parse_url(`*url*`)`

## <a name="arguments"></a>인수

* *url*: URL 또는 url의 쿼리 부분을 나타내는 문자열입니다.

## <a name="returns"></a>반환

URL 구성 요소 (구성표, 호스트, 포트, 경로, 사용자 이름, 암호, 쿼리 매개 변수, 조각)를 포함 하는 [dynamic](./scalar-data-types/dynamic.md) 형식의 개체입니다.

## <a name="example"></a>예제

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

결과

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