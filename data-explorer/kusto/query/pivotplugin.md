---
title: 피벗 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 피벗 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a046cc369dd466defa50916ee78b2c29f5f88ea0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373228"
---
# <a name="pivot-plugin"></a>피벗 플러그 인

입력 테이블의 한 열에 있는 고유 값을 출력 테이블의 여러 열로 변환 하 여 테이블을 회전 하 고 최종 출력에서 원하는 나머지 열 값에 필요한 집계를 수행 합니다.

```kusto
T | evaluate pivot(PivotColumn)
```

**구문**

`T | evaluate pivot(`*pivotColumn* `[, ` *aggregationFunction* `] [,` *column1* `[,` *column2* ...`]])`

**인수**

* *pivotColumn*: 회전할 열입니다. 이 열의 각 고유 값은 출력 테이블의 열이 됩니다.
* *집계 함수*: (선택 사항) 입력 테이블의 여러 행을 출력 테이블의 단일 행에 집계 합니다. 현재 지원 되는 함수는 `min()` , `max()` ,,,,,, `any()` `sum()` `dcount()` `avg()` `stdev()` `variance()` 및 `count()` 입니다 (기본값은 `count()` ).
* *column1*, *column2*, ...: (옵션) 열 이름 출력 테이블에는 지정 된 각 열 마다 추가 열이 포함 됩니다. 기본값: 피벗 열 및 집계 열 이외의 모든 열

**반환**

피벗은 지정 된 열 (*column1*, *column2*, ...)과 피벗 열의 모든 고유 값을 사용 하 여 회전 된 테이블을 반환 합니다. 피벗 된 열의 각 셀에는 집계 함수 계산이 포함 됩니다.

**참고**

플러그 인의 출력 스키마는 `pivot` 데이터를 기반으로 하므로 쿼리는 두 실행에 대해 다른 스키마를 생성할 수 있습니다. 이는 압축을 푼 열을 참조 하는 쿼리가 언제 든 지 ' 중단 ' 될 수도 있음을 의미 합니다. 이러한 이유 때문에 자동화 작업에는이 플러그 인을 사용 하지 않는 것이 좋습니다.

**예**

### <a name="pivot-by-a-column"></a>열을 기준으로 피벗

' T r u e '로 시작 하는 각 EventType 및 상태에 대해이 상태에서이 형식의 이벤트 수를 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|높은 바람|0|95|
|매우 콜드/바람 Chill|0|10|
|강한 바람|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>집계 함수를 사용 하는 열을 기준으로 피벗 합니다.

' AR '로 시작 하는 각 EventType 및 상태에 대해 직접 deaths의 총 수를 표시 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|아칸 사스|애리조나|
|---|---|---|
|과도 한 비|1|0|
|뇌우를 동반한 바람|1|0|
|번개|0|1|
|갑작스러운 홍수|0|6|
|강한 바람|1|0|
|전달을|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>집계 함수 및 하나의 추가 열을 포함 하는 열을 기준으로 피벗 합니다.

결과는 이전 예제와 동일 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|아칸 사스|애리조나|
|---|---|---|
|과도 한 비|1|0|
|뇌우를 동반한 바람|1|0|
|번개|0|1|
|갑작스러운 홍수|0|6|
|강한 바람|1|0|
|전달을|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>피벗 열, 집계 함수 및 여러 개의 추가 열을 지정 합니다.

각 이벤트 유형, 원본 및 상태에 대해 직접 deaths의 수를 합 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|원본|아칸 사스|애리조나|
|---|---|---|---|
|과도 한 비|긴급 상황 관리자|1|0|
|뇌우를 동반한 바람|긴급 상황 관리자|1|0|
|번개|신문|0|1|
|갑작스러운 홍수|숙련된 관찰자|0|2|
|갑작스러운 홍수|미디어 브로드캐스트|0|3|
|갑작스러운 홍수|신문|0|1|
|강한 바람|사법 기관|1|0|
|전달을|신문|3|0|
