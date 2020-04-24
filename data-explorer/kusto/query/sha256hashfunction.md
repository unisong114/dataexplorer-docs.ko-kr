---
title: hash_sha256() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 hash_sha256()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507602"
---
# <a name="hash_sha256"></a>hash_sha256()

입력 값에 대한 sha256 해시 값을 반환합니다.

**구문**

`hash_sha256(`*소스*`)`

**인수**

* *출처*: 해시할 값입니다.

**반환**

육각 문자열로 인코딩된 지정된 스칼라의 sha256 해시 값입니다(각각 두 문자열은 0에서 255 사이의 단일 육각 번호를 나타냅니다).

> [!WARNING]
> 이 함수(SHA256)에서 사용되는 알고리즘은 나중에 수정되지 않도록 보장되지만 계산하기가 매우 복잡합니다. 단일 쿼리 기간 동안 "경량" 해시 함수가 필요한 사용자는 대신 [해시()](./hashfunction.md) 함수를 사용하는 것이 좋습니다.

**예**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

다음 예제에서는 hash_sha256 함수를 사용하여 데이터의 StartTime 열에서 쿼리를 실행합니다.

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```