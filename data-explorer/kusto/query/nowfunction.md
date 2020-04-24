---
title: 지금() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 지금()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512056"
---
# <a name="now"></a>now()

지정된 시간 범위로 선택적으로 오프셋된 현재 UTC 클럭 시간을 반환합니다.
이 함수를 문에 여러 번 사용할 수 있으며 참조하는 시계 시간은 모든 인스턴스에 대해 동일합니다.

```kusto
now()
now(-2d)
```

**구문**

`now(`[*오프셋*]`)`

**인수**

* *오프셋* `timespan`: A . 기본값: 0.

**반환**

`datetime`(으)로 반환되는 현재 UTC 시계 시간입니다.

`now()` + *오프셋* 

**예제**

조건자에 의해 식별된 이벤트 이후 간격을 결정;

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```