---
title: reverse ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 역방향 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dc348643ff6e098b2291e69d68ca817c1f5af9c2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243007"
---
# <a name="reverse"></a>reverse()

함수는 입력 문자열의 순서를 반대로 바꿉니다.
입력 값이 형식이 아닌 경우 `string` 함수는 값을 강제로 형식으로 캐스팅 합니다 `string` .

## <a name="syntax"></a>구문

`reverse(`*원본*`)`

## <a name="arguments"></a>인수

* *원본*: 입력 값입니다.  

## <a name="returns"></a>반환

문자열 값의 반대 순서입니다.

## <a name="examples"></a>예

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54.321|Z 0000000.00:00:21T51-01-7102|00:00:30|
