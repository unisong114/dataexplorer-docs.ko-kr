---
title: parse_urlquery ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_urlquery ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244687"
---
# <a name="parse_urlquery"></a>parse_urlquery()

쿼리 매개 변수를 포함 하는 개체를 반환 합니다 `dynamic` .

## <a name="syntax"></a>구문

`parse_urlquery(`*쿼리입니다*`)`

## <a name="arguments"></a>인수

* *쿼리*: 문자열이 url 쿼리를 나타냅니다.

## <a name="returns"></a>반환

쿼리 매개 변수를 포함 하는 [dynamic](./scalar-data-types/dynamic.md) 형식의 개체입니다.

## <a name="example"></a>예제

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

결과는 다음과 같습니다.

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**참고**

* 입력 형식은 URL 쿼리 표준 (키 = 값& ...)을 따라야 합니다.
 