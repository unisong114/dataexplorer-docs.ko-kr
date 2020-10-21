---
title: countof ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 countof)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5204aff94f2fd6e6c824f66bdc30000b46c05501
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249243"
---
# <a name="countof"></a>countof()

문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

## <a name="syntax"></a>구문

`countof(`*텍스트* `,` *검색* [ `,` *kind*]`)`

## <a name="arguments"></a>인수

* *text*: 문자열입니다.
* *검색*: *텍스트*내에서 일치 하는 일반 문자열 또는 [정규식](./re2.md) 입니다.
* *kind*: `"normal"|"regex"` 기본값 `normal` 

## <a name="returns"></a>반환

검색 문자열이 컨테이너에서 일치시킬 수 있는 횟수입니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

## <a name="examples"></a>예

|함수 호출|결과|
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3(2 아님!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    