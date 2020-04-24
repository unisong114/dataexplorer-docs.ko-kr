---
title: 이전() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 이전()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519094"
---
# <a name="ago"></a>ago()

현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다.

```kusto
ago(1h)
ago(1d)
```

`now()`와(과) 마찬가지로, 이 함수를 문에 여러 번 사용할 수 있으며 참조하는 UTC 시계 시간은 모든 인스턴스에 대해 동일합니다.

**구문**

`ago(`*a_timespan*`)`

**인수**

* *a_timespan*: 현재 UTC 시계 시간(`now()`)에서 뺄 간격입니다.

**반환**

`now() - a_timespan`

**예제**

과거 시간에 타임스탬프를 가진 모든 행:

```kusto
T | where Timestamp > ago(1h)
```