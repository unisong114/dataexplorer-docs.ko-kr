---
title: percentile_tdigest ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 percentile_tdigest ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 7111f34fcd42e025b22960aa013310d7e4b672fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252176"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

`tdigest` [Tdigest ()](tdigest-aggfunction.md) 또는 [tdigest_merge ()](tdigest-merge-aggfunction.md)에 의해 생성 된 결과에서 백분위 수 결과를 계산 합니다.

## <a name="syntax"></a>구문

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*] ... [ `,` *PercentileN*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*동적 배열*`)`

## <a name="arguments"></a>인수

* *Expr*: [`tdigest`](tdigest-aggfunction.md) 또는 [tdigest_merge ()](tdigest-merge-aggfunction.md)에서 생성 된 식입니다.
* *백분위* 수는 백분위 수를 지정 하는 double 상수입니다.
* *typeLiteral*: 선택적 형식 리터럴 (예: `typeof(long)` )입니다. 제공 된 경우 결과 집합은이 형식이 됩니다. 
* *동적 배열*: 정수 또는 부동 소수점 숫자의 동적 배열에서 백분위 수의 목록입니다.

## <a name="returns"></a>반환

의 각 값에 대 한 백분위 수/percentilesw 값입니다 *`Expr`* .

**팁**

* 함수는 적어도 1%를 받아야 합니다. *Percentile1* [ `,` *Percentile2*] ... 위의 구문을 참조 하세요. [ `,` *PercentileN*])를 반환 하 고 결과를 포함 하는 동적 배열이 생성 됩니다. (예: [`percentiles()`](percentiles-aggfunction.md) )
  
* 1%만 제공 되 고 형식이 제공 된 경우 결과는 해당 백분율의 결과와 함께 제공 된 동일한 형식의 열이 됩니다. 이 경우 모든 `tdigest` 함수는 해당 형식 이어야 합니다.

* 에 *`Expr`* `tdigest` 다른 형식의 함수가 포함 되어 있으면 형식을 제공 하지 않습니다. 결과는 동적 형식이 됩니다. 아래 예제를 참조하세요.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|6200만|
|1억1000만|
|120만|
|250000|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0, 0, 0]|
|[0, 0, 62000000]|
|[0, 0, 110000000]|
|[0, 0, 1200000]|
|[0, 0, 250000]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[6200만]|
|["2007-12-20T11:30:00.0000000 Z"]|
|["2007-12-31T23:59:00.0000000 Z"]|
