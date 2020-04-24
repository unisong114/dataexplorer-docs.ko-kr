---
title: 해시() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 해시()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514147"
---
# <a name="hash"></a>hash()

입력 값에 대한 해시 값을 반환합니다.

**구문**

`hash(`*소스* `,` [ *모드*]`)`

**인수**

* *출처*: 해시할 값입니다.
* *mod*: 해시 결과에 적용할 선택적 모듈 값으로 출력 값이 `0` *모드와 모드* 사이에 있도록 합니다 - 1

**반환**

지정된 스칼라의 해시 값은 지정된 모드 값(지정된 경우)을 modulo로 합니다.

> [!WARNING]
> 해시를 계산하는 데 사용되는 알고리즘은 xxhash입니다.
> 이 알고리즘은 나중에 변경될 수 있으며 단일 쿼리 내에서 이 메서드의 모든 호출이 동일한 알고리즘을 사용한다는 것이 유일한 보장입니다.
> 따라서 사용자는 테이블에 결과를 `hash()` 저장하지 않는 것이 좋습니다. 지속 해시 값이 필요한 경우 [대신 hash_sha256()를](./sha256hashfunction.md) 사용하는 것이 좋습니다(하지만 계산하는 `hash()`것이 훨씬 더 복잡합니다).

**예**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

다음 예제에서는 해시 함수를 사용하여 데이터의 10%에 대한 쿼리를 실행하며, 값이 균일하게 분산되어 있다고 가정할 때 데이터를 샘플링하기 위해 해시 함수를 사용하는 것이 좋습니다(이 예제에서 StartTime 값).

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```