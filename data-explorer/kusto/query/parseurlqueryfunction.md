---
title: parse_urlquery() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_urlquery()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744631"
---
# <a name="parse_urlquery"></a>parse_urlquery()

개체에 `dynamic` 쿼리 매개 변수가 포함 된 반환 합니다.

**구문**

`parse_urlquery(`*쿼리*`)`

**인수**

* *쿼리*: 문자열은 URL 쿼리를 나타냅니다.

**반환**

쿼리 매개 변수를 포함하는 [동적](./scalar-data-types/dynamic.md) 형식의 개체입니다.

**예제**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

다음과 같은 결과가 발생합니다.

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**참고 사항**

* 입력 형식은 URL 쿼리 표준을 따라야 합니다(key=값&...)
 