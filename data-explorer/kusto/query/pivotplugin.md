---
title: 피벗 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 피벗 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4ec5a94483ade822280ee4a71106c214bb4b9a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511104"
---
# <a name="pivot-plugin"></a>피벗 플러그인

입력 테이블의 한 열에서 고유한 값을 출력 테이블의 여러 열로 변환하여 테이블을 회전하고 최종 출력에 필요한 나머지 열 값에 필요한 집계를 수행합니다.

```kusto
T | evaluate pivot(PivotColumn)
```

**구문**

`T | evaluate pivot(`*피벗열*`[, `*집계기능*`] [,`*열1* `[,` *열2* ...`]])`

**인수**

* *피벗열*: 회전할 열입니다. 이 열의 각 고유 값은 출력 테이블의 열이 됩니다.
* *집계 함수*: (선택 사항) 입력 테이블의 여러 행을 출력 테이블의 단일 행에 집계합니다. 현재 지원되는 `min()`기능: `any()` `sum()`. `dcount()` `max()` `avg()`. `stdev()` `variance()` `count()` `count()`
* *column1*, *column2*, ...: (선택 사항) 열 이름입니다. 출력 테이블에는 지정된 각 열당 추가 열이 포함됩니다. 기본값: 피벗 된 열 및 집계 열을 제외한 모든 열입니다.

**반환**

피벗은 지정된*열(열1,* *열2*, ...) 및 피벗 열의 모든 고유 값으로 회전된 테이블을 반환합니다. 피벗된 열의 각 셀에는 집계 함수 계산이 포함됩니다.

**참고**

`pivot` 플러그인의 출력 스키마는 데이터를 기반으로 하므로 쿼리는 두 실행에 대해 서로 다른 스키마를 생성할 수 있습니다. 즉, 압축 해제된 열을 참조하는 쿼리는 언제든지 '끊어질'수 있습니다. 이러한 이유로 인해 - 자동화 작업에이 플러그인을 사용하는 것이 좋습니다되지 않습니다.

**예**

### <a name="pivot-by-a-column"></a>열로 피벗

'AL'로 시작하는 각 EventType 및 상태에 대해 이 상태에서 이 유형의 이벤트 수를 계산합니다.

```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|EventType|ALABAMA|알래스카|
|---|---|---|
|뇌우를 동반한 바람|352|1|
|강풍|0|95|
|극한의 추위/바람의 차가운|0|10|
|강한 바람|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>집계 함수가 있는 열로 피벗합니다.

'AR'로 시작하는 각 이벤트 유형 및 상태에 대해 직접 사망의 총 수를 표시합니다.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|아칸소|애리조나|
|---|---|---|
|호우|1|0|
|뇌우를 동반한 바람|1|0|
|번개|0|1|
|갑작스러운 홍수|0|6|
|강한 바람|1|0|
|열|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>집계 함수와 단일 추가 열이 있는 열로 피벗합니다.

결과는 이전 예제와 동일합니다.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|아칸소|애리조나|
|---|---|---|
|호우|1|0|
|뇌우를 동반한 바람|1|0|
|번개|0|1|
|갑작스러운 홍수|0|6|
|강한 바람|1|0|
|열|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>피벗 된 열, 집계 함수 및 여러 추가 열을 지정합니다.

각 이벤트 유형, 소스 및 상태에 대해 직접 사망 횟수를 합산합니다.

```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|원본|아칸소|애리조나|
|---|---|---|---|
|호우|긴급 상황 관리자|1|0|
|뇌우를 동반한 바람|긴급 상황 관리자|1|0|
|번개|신문|0|1|
|갑작스러운 홍수|숙련된 관찰자|0|2|
|갑작스러운 홍수|브로드캐스트 미디어|0|3|
|갑작스러운 홍수|신문|0|1|
|강한 바람|사법 기관|1|0|
|열|신문|3|0|