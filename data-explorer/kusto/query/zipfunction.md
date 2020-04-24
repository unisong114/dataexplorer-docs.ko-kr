---
title: zip() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 zip()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504236"
---
# <a name="zip"></a>zip()

함수는 `zip` 임의의 수의 `dynamic` 배열을 허용하고 각 요소가 동일한 인덱스의 입력 배열의 요소를 포함하는 배열인 배열을 반환합니다.

**구문**

`zip(`*배열1* `,` *배열2*`, ... )`

**인수**

2에서 16 사이의 동적 배열.

**예**

다음 예제는 `[[1,2],[3,4],[5,6]]`을 반환합니다.

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

다음 예제는 `[["A",{}], [1,"B"], [1.5, null]]`을 반환합니다.

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

다음 예제는 `[[1,"one"],[2,"two"],[3,"three"]]`을 반환합니다.

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```