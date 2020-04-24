---
title: make_string() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_string()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512634"
---
# <a name="make_string"></a>make_string()

유니코드 문자에 의해 생성된 문자열을 반환합니다.
    
**구문**

`make_string (`*아르그1* [, *아르그N]...*`)`

**인수**

* *아르그1* ... *ArgN* : 정수(int 또는 long) 또는 정수 의 배열을 포함하는 동적 값인 식입니다.

* 이 함수는 최대 64개의 인수를 수신합니다. 

**반환**

이 함수에 대한 인수에서 코드포인트 값이 제공되는 유니코드 문자로 만들어진 문자열을 반환합니다. 입력은 유효한 유니코드 코드포인트로 구성되어야 합니다.
인수가 유니코드 char에 매핑되지 않으면 함수는 null을 반환합니다.

**예**

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