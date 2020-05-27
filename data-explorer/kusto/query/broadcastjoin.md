---
title: 브로드캐스트 조인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 브로드캐스트 조인에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e64413047ceb83860f7ea47a1540ae99faa7ec55
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863220"
---
# <a name="broadcast-join"></a>브로드캐스트 조인

현재 일반 조인은 단일 클러스터 노드에서 실행 됩니다.
브로드캐스트 조인은 클러스터 노드를 통해 배포 되는 조인 실행 전략입니다. 이 전략은 조인의 왼쪽이 작은 경우 (최대 100 K 레코드)에 유용 합니다. 이 경우 브로드캐스트 조인은 일반 조인 보다 성능이 더 우수 합니다.

조인의 왼쪽에 작은 데이터 집합이 있는 경우 다음 구문을 사용 하 여 브로드캐스트 모드에서 join을 실행할 수 있습니다 (힌트. 전략 = 브로드캐스트).

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

조인 뒤에와 같은 다른 연산자가 오는 시나리오에서는 성능 향상이 더 두드러집니다 `summarize` . 예를 들면 다음과 같습니다.

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```