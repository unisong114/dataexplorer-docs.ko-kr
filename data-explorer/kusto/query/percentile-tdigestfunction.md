---
title: percentile_tdigest() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 percentile_tdigest()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511240"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

tdigest [결과(tdigest()](tdigest-aggfunction.md) 또는 [tdigest_merge()](tdigest-merge-aggfunction.md)생성된 tdigest 결과에서 백분위수 결과를 계산합니다.

**구문**

`percentile_tdigest(`*Expr* `,` *백분위수1* [`,` *문자Lite]*`)`

`percentiles_array_tdigest(`*Expr* `,` *백분위수1* [`,` *백분위수2*] ... [`,` *백분위수*]`)`

`percentiles_array_tdigest(`*Expr* `,` *동적 배열*`)`

**인수**

* *예시*: [tdigest](tdigest-aggfunction.md) 또는 [tdigest_merge()에](tdigest-merge-aggfunction.md)의해 생성된 식 .
* *백분위수는* 백분위수를 지정하는 이중 상수입니다.
* *typeLiteral*: 선택적 문자 리터럴(예: `typeof(long)`)입니다. 제공된 경우 결과 집합은 이 유형입니다. 
* *동적 배열*: 정수 또는 부동 포인트 번호의 동적 배열의 백분위수 목록

**반환**

*Expr의*각 값의 백분위수/백분위수 값입니다.

**팁**

1) 함수는 적어도 1 %를 받아야합니다 (그리고 어쩌면 더, 위의`,` 구문을 참조하십시오 : *백분위수1* [ *백분위수2*] ... [`,` *백분위수*]) 결과가 포함된 동적 배열이 됩니다. (등) [`percentiles()`](percentiles-aggfunction.md)
  
2) 1%만 제공되었고 형식도 제공된 경우 결과는 해당 백분율의 결과와 함께 제공되는 동일한 유형의 열이 됩니다(이 경우 모든 tdigests는 해당 형식이어야 합니다).

3) *Expr에* 다른 유형의 다이제스트가 포함된 경우 형식을 제공하지 않으며 결과는 동적 형식이 됩니다. (아래 예제 참조).

**예**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11:30:00.00000000Z"]|
|["2007-12-31T23:59:00.00000000Z"]|