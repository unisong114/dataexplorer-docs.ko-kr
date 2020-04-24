---
title: countof() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 countof()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516901"
---
# <a name="countof"></a>countof()

문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**구문**

`countof(`*텍스트* `,` *search* 검색`,` [ *종류*]`)`

**인수**

* *텍스트*: 문자열입니다.
* *검색*: *텍스트*내부에 일치하는 일반 문자열 또는 [정규식입니다.](./re2.md)
* *종류* `"normal"|"regex"` : `normal`기본값 . 

**반환**

검색 문자열이 컨테이너에서 일치시킬 수 있는 횟수입니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

**예**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3(2 아님!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    