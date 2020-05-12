---
title: hash ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 해시 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a44f817ea57a114400f45e9ca2a841150b4590a6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226808"
---
# <a name="hash"></a>hash()

입력 값에 대 한 해시 값을 반환 합니다.

**구문**

`hash(`*원본* [ `,` *mod*]`)`

**인수**

* *source*: 해시할 값입니다.
* *mod*: 출력 값이 `0` 및 *mod* -1 사이인 해시 결과에 적용할 선택적 모듈 값입니다.

**반환**

지정 된 스칼라의 해시 값입니다. 지정 된 mod 값 (지정 된 경우)의 나머지 부분입니다.

> [!WARNING]
> 해시를 계산 하는 데 사용 되는 알고리즘은 xxhash입니다.
> 이 알고리즘은 나중에 변경 될 수 있으며 단일 쿼리 내에서이 메서드를 호출할 때마다 동일한 알고리즘을 사용 한다는 것만을 보장 합니다.
> 따라서 사용자는의 결과를 테이블에 저장 하지 않는 것이 좋습니다 `hash()` . 해시 값을 유지 해야 하는 경우에는 대신 [hash_sha256 ()](./sha256hashfunction.md) 를 사용 하는 것이 좋습니다. 그러나 보다 계산 하기가 훨씬 복잡 `hash()` 합니다.

**예**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

다음 예에서는 hash 함수를 사용 하 여 데이터의 10%에 대 한 쿼리를 실행 합니다. 값이 균일 하 게 분산 되었다고 가정할 때 해시 함수를 사용 하 여 데이터를 샘플링 하는 것이 좋습니다 (이 예에서는 StartTime 값).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
