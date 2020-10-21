---
title: make_string ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 make_string ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8f3497e10c15bfd6df0337758d8dc3002419fa1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252231"
---
# <a name="make_string"></a>make_string()

유니코드 문자로 생성 된 문자열을 반환 합니다.
    
## <a name="syntax"></a>구문

`make_string (`*Arg1*[, *argn*] ... `)`

## <a name="arguments"></a>인수

* *Arg1* ... *Argn*: 정수 (int 또는 long) 또는 정수 배열을 포함 하는 동적 값의 식입니다.

* 이 함수는 최대 64 개의 인수를 받습니다.

## <a name="returns"></a>반환

이 함수에 대 한 인수에서 코드 포인트 값을 제공 하는 유니코드 문자로 이루어진 문자열을 반환 합니다. 입력은 유효한 유니코드 codepoints로 구성 되어야 합니다.
인수가 유니코드 문자에 매핑되지 않은 경우 함수는를 반환 `null` 합니다.

## <a name="examples"></a>예

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>t<br>o|
