---
title: percentrank_tdigest() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 percentrank_tdigest()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511189"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

순위가 집합 크기의 백분율로 표현되는 집합에서 값의 대략적인 순위를 계산합니다. 이 함수는 백분위수의 역으로 볼 수 있습니다.

**구문**

`percentrank_tdigest(`*T다이제스트* `,` *익스프레*`)`

**인수**

* *TDigest*: [tdigest()](tdigest-aggfunction.md) 또는 [tdigest_merge()에](tdigest-merge-aggfunction.md)의해 생성된 식입니다.
* *Expr*: 백분율 순위 계산에 사용할 값을 나타내는 표현식입니다.

**반환**

데이터 집합의 값 백분율 순위입니다.

**팁**

1) 두 번째 매개 변수의 형식과 tdigest의 요소 형식은 동일해야 합니다.

2) 첫 번째 매개 변수는 [tdigest()](tdigest-aggfunction.md) 또는 [tdigest_merge()](tdigest-merge-aggfunction.md)

**예**

4490$의 피해 재산의 percentrank_tdigest()를 얻는 것은 ~ 85%입니다.

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|열1|
|---|
|85.0015237192293|


손상 속성에 백분위수 85를 사용하면 4490 $를 제공해야합니다.

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|