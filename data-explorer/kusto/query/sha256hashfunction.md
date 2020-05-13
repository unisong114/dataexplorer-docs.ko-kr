---
title: hash_sha256 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_sha256 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372328"
---
# <a name="hash_sha256"></a>hash_sha256()

입력 값의 sha256 해시 값을 반환 합니다.

**구문**

`hash_sha256(`*원본*`)`

**인수**

* *source*: 해시할 값입니다.

**반환**

16 진수 문자열로 인코딩된 지정 된 스칼라의 sha256 해시 값입니다 .이 값은 문자 문자열 (0에서 255 사이의 단일 16 진수를 나타냄)입니다.

> [!WARNING]
> 이 함수 (SHA256)에서 사용 하는 알고리즘은 나중에 수정 되지 않지만 계산 하기는 매우 복잡 합니다. 단일 쿼리 기간 동안 "경량" 해시 함수를 사용 해야 하는 사용자는 대신 함수 [해시 ()](./hashfunction.md) 를 사용 하는 것이 좋습니다.

**예**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

다음 예에서는 hash_sha256 함수를 사용 하 여 데이터의 StartTime 열에 대해 쿼리를 실행 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
