---
title: parse_urlquery ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_urlquery ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346272"
---
# <a name="parse_urlquery"></a>parse_urlquery()

쿼리 매개 변수를 포함 하는 개체를 반환 합니다 `dynamic` .

## <a name="syntax"></a>Syntax

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
 