---
title: zip ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 zip ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33b914babb8c197f997326cd6dd44654c05d1d9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253141"
---
# <a name="zip"></a>zip()

`zip`함수는 임의의 수의 `dynamic` 배열을 허용 하 고, 각 요소가 동일한 인덱스의 입력 배열 요소를 포함 하는 배열인 배열을 반환 합니다.

## <a name="syntax"></a>구문

`zip(`*array1* 방법 `,` *array2* 가는`, ... )`

## <a name="arguments"></a>인수

2에서 16 개의 동적 배열 사이입니다.

## <a name="examples"></a>예

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