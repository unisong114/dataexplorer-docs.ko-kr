---
title: rank_tdigest() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 rank_tdigest()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510543"
---
# <a name="rank_tdigest"></a>rank_tdigest()

집합에서 값의 대략적인 순위를 계산합니다. `v` 집합의 `S` 값 순위는 작거나 같을 `S` `v`구성원의 개수로 `S` 정의되며, 이 값은 tdigest로 표시됩니다.

**구문**

`rank_tdigest(`*T다이제스트* `,` *익스프레*`)`

**인수**

* *TDigest*: [tdigest()](tdigest-aggfunction.md) 또는 [tdigest_merge()](tdigest-merge-aggfunction.md)
* *ExPR*: 순위 계산에 사용할 값을 나타내는 표현식입니다.

**반환**

데이터 집합의 foreach 값의 순위입니다.

**팁**

1) 순위를 얻으려는 값은 tdigest와 동일한 형식이어야 합니다.

**예**

정렬된 목록(1-1000)에서 685의 순위는 인덱스입니다.

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

이 쿼리는 모든 손상 속성 비용에 대한 값 4490 $의 순위를 계산합니다.

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

순위의 예상 백분율을 (설정된 크기로 나누어) :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


손상 속성 비용의 백분위수 85는 4490 $입니다 :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


