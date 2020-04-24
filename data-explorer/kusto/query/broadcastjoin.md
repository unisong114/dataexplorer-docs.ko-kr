---
title: 브로드캐스트 조인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 브로드캐스트 조인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517309"
---
# <a name="broadcast-join"></a>브로드캐스트 조인

오늘날 일반 조인은 단일 클러스터 노드에서 실행됩니다.
브로드캐스트 조인은 클러스터 노드를 통해 배포하는 조인의 실행 전략이며, 조인의 왼쪽이 작을 때(최대 100K 레코드), 이 경우 조인은 일반 조인보다 성능이 더 높습니다.

조인의 왼쪽이 작은 데이터 집합인 경우 다음 구문(hint.strategy = 브로드캐스트)을 사용하여 브로드캐스트 모드에서 조인을 실행할 수 있습니다.

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

조인 다음에 와 같은 `summarize`다른 연산자가 뒤따르는 시나리오에서는 성능 향상이 더 두드러집니다. 예를 들어 이 쿼리에서 다음을 수행합니다.

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```