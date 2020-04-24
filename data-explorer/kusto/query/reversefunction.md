---
title: 역() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 역()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e95246e0586dff7dd89dc2658c7fae08b1bbaddf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510305"
---
# <a name="reverse"></a>reverse()

함수는 입력 문자열을 반대로 만듭니다.

입력 값이 문자열 형식이 아닌 경우 함수는 강제로 값을 문자열에 캐스팅합니다.

**구문**

`reverse(`*소스*`)`

**인수**

* *소스*: 입력 값입니다.  

**반환**

문자열 값의 역순입니다.

**예**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|Rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|지크스뷔츠RQPONMLKJIHGFedCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|시간 범위|
|---|---|---|---|
|54321|54.321|Z0000000.00:00:21T51-01-7102|00:00:30|




 