---
title: to_utf8() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 to_utf8()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505834"
---
# <a name="to_utf8"></a>to_utf8()

입력 문자열의 유니코드 문자의 동적 배열(make_string 역작업)을 반환합니다.

**구문**

`to_utf8(`*소스*`)`

**인수**

* *소스*: 변환할 소스 문자열입니다.

**반환**

이 함수에 제공된 문자열을 구성하는 유니코드 문자의 동적 배열을 반환합니다.
) [`make_string()`](makestringfunction.md)참조)

**예**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|도착|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|도착|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
