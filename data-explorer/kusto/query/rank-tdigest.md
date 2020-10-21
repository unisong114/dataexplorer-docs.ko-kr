---
title: rank_tdigest ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 rank_tdigest ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: bc0fff9d70c8260781332be61c701aaaca364555
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244849"
---
# <a name="rank_tdigest"></a>rank_tdigest()

집합에 있는 값의 대략적인 순위를 계산 합니다. `v`집합의 값 순위 `S` 는 보다 작거나 같은의 멤버 수로 정의 되며 `S` `v` 는로 `S` 표시 됩니다 `tdigest` .

## <a name="syntax"></a>구문

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

## <a name="arguments"></a>인수

* *Tdigest*: [tdigest ()](tdigest-aggfunction.md) 또는 [tdigest_merge ()](tdigest-merge-aggfunction.md) 에 의해 생성 된 식입니다.
* *Expr*: 순위 계산에 사용할 값을 나타내는 식입니다.

## <a name="returns"></a>반환

데이터 집합의 rank foreach 값입니다.

**팁**

1) 순위를 가져오려는 값은와 같은 형식 이어야 합니다 `tdigest` .

## <a name="examples"></a>예

정렬 된 목록 (1-1000)에서 685의 순위는 인덱스입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

이 쿼리는 모든 손상 속성 비용에 대해 $4490 값의 순위를 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

크기를 설정 하 여 순위에 대 한 예상 비율을 가져옵니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


손상 속성 비용의 백분위 수 85은 $4490입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


