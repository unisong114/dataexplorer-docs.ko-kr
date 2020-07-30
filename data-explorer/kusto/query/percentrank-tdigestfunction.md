---
title: percentrank_tdigest ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 percentrank_tdigest ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 33eb35e51f403a3c0b7a2f030604b12c705221ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346170"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

집합에 있는 값의 대략적인 순위를 계산 합니다. 여기서 rank는 집합 크기의 백분율로 표현 됩니다.
이 함수는 백분위 수의 역함수로 볼 수 있습니다.

## <a name="syntax"></a>Syntax

`percentrank_tdigest(`*Tdigest* `,` *Expr*`)`

## <a name="arguments"></a>인수

* *Tdigest*: [tdigest ()](tdigest-aggfunction.md) 또는 [tdigest_merge ()](tdigest-merge-aggfunction.md)에 의해 생성 된 식입니다.
* *Expr*: 백분율 순위 계산에 사용할 값을 나타내는 식입니다.

## <a name="returns"></a>반환

데이터 집합에 있는 값의 백분율 순위입니다.

**팁**

1) 두 번째 매개 변수의 형식과의 요소 형식이 `tdigest` 동일 해야 합니다.

2) 첫 번째 매개 변수는 [tdigest ()](tdigest-aggfunction.md) 또는 [tdigest_merge ()](tdigest-merge-aggfunction.md) 에서 생성 된 tdigest 여야 합니다.

## <a name="examples"></a>예제

$4490 값이 인 손상 속성의 percentrank_tdigest () 가져오기 (~ 85%):

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|열1|
|---|
|85.0015237192293|


손상 속성에 대해 백분위 수 85을 사용 하면 $4490를 제공 해야 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
