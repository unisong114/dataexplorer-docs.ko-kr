---
title: format_bytes() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 format_bytes()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515167"
---
# <a name="format_bytes"></a>format_bytes()

숫자의 형식을 바이트로 데이터 크기를 나타내는 문자열로 지정합니다.

```kusto
format_bytes(1024) == '1 KB'"
```

**구문**

`format_bytes(``,` *[정밀도* `,` *[단위]]* *value*`)`

**인수**

* `value`: 데이터 크기로 서식이 지정되는 숫자입니다.
* `precision`: (선택 사항) 값이 반올림되는 숫자 수입니다. (기본값은 0)입니다.
* `units`: (선택 사항) 대상 데이터 크기의 단위 문자열`Bytes` `KB`서식을 사용합니다 ( `MB`, " `GB`, `TB`" `PB`). 매개 변수가 비어 있는 경우 - 단위는 입력 값에 따라 자동으로 선택됩니다.

**반환**

형식 이 있는 문자열 결과입니다.

**예**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564바이트|10.1 KB|19 MB|19.08 MB|19541년 KB|