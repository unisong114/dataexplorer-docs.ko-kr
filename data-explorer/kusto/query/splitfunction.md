---
title: 분할() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 split()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6e3935c524056afd27eb0d5e2d80925e072c8fa7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507466"
---
# <a name="split"></a>split()

지정된 구분 기호에 따라 지정된 문자열을 분할하고 포함된 하위 문자열이 있는 문자열 배열을 반환합니다.

선택적으로 특정 부분 문자열(있는 경우)을 반환할 수 있습니다.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

**구문**

`split(`*소스* `,` *구분 기호* [`,` *요청인덱스]*`)`

**인수**

* *소스*: 지정된 구분 기호에 따라 분할될 소스 문자열입니다.
* *delimiter*: 소스 문자열을 분할하기 위해 사용될 구분 기호입니다.
* *requestedIndex*: 선택적 0부터 시작하는 인덱스 `int`입니다. 제공된 경우, 반환되는 문자열 배열은 요청된 부분 문자열(있는 경우)을 포함합니다. 

**반환**

지정된 구분 기호로 구분되는 지정된 소스 문자열의 부분 문자열을 포함하는 문자열의 배열입니다.

**예**

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```