---
title: funnel_sequence_completion 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 funnel_sequence_completion 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 7f168841db2df47e4e3a192b75585a1fe4d83718
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514776"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion 플러그 인

다른 기간 비교 내에서 완료된 시퀀스 단계의 유입경로를 계산합니다.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**구문**

*T* `| evaluate` `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Step* `,` *StateColumn* `,` *Sequence* `,` *MaxSequenceStepWindows* *IdColumn* IdColumn 타임라인 열 시작 종료 단계 상태열 시퀀스 MaxSequenceStepWindows `funnel_sequence_completion(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *IdColum*: 열 참조, 소스 식에 있어야 합니다.
* *TimelineColumn*: 타임라인을 나타내는 열 참조가 소스 식에 있어야 합니다.
* *시작*: 분석 시작 기간의 스칼라 상수 값
* *끝*: 해석 종료 기간의 스칼라 상수 값
* *단계*: 분석 단계 기간의 스칼라 상수 값(빈) 
* *StateColumn*: 상태를 나타내는 열 참조는 소스 식에 있어야 합니다.
* *시퀀스*: 시퀀스 값이 있는 상수 `StateColumn`동적 배열(값이 위로 조회됩니다)
* *MaxSequenceStepWindows*: 시퀀스의 첫 번째 및 마지막 순차 적 단계 사이의 최대 허용 시간 범위의 값을 가진 스칼라 상수 동적 배열, 배열의 각 창 (기간)은 깔때기 분석 결과를 생성합니다.

**반환**

분석된 시퀀스에 대한 깔때기 다이어그램을 구성하는 데 유용한 단일 테이블을 반환합니다.

* 타임라인열: 분석된 시간 창
* `StateColumn`: 시퀀스의 상태입니다.
* 기간: 시퀀스의 제1 단계로부터 측정된 유입경로 서열에서 단계를 완료하기 위해 허용된 최대 기간(창). *MaxSequenceStepWindows의* 각 값은 별도의 기간으로 깔때기 분석을 생성합니다. 
* dcount: 첫 `IdColumn` 번째 시퀀스 상태에서 값으로 전환된 `StateColumn`시간 창의 고유한 개수입니다.

**예**

### <a name="exploring-storm-events"></a>폭풍 이벤트 탐색 

다음 쿼리는 시퀀스의 완료 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 깔때기를 확인합니다: 1시간, 4시간, 1일의 "전체" 시간입니다. 

```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|StartTime|EventType|기간|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|우박|01:00:00|2877|
|2007-01-01 00:00:00.0000000|토네이도|01:00:00|208|
|2007-01-01 00:00:00.0000000|뇌우를 동반한 바람|01:00:00|87|
|2007-01-01 00:00:00.0000000|우박|04:00:00|2877|
|2007-01-01 00:00:00.0000000|토네이도|04:00:00|231|
|2007-01-01 00:00:00.0000000|뇌우를 동반한 바람|04:00:00|141|
|2007-01-01 00:00:00.0000000|우박|1.00:00:00|2877|
|2007-01-01 00:00:00.0000000|토네이도|1.00:00:00|244|
|2007-01-01 00:00:00.0000000|뇌우를 동반한 바람|1.00:00:00|155|

결과 이해:  
결과 3 깔때기 (기간: 1 시간, 4 시간 및 1 일), 각 유입경로 단계에 대 한 에피소드 Id의 별개의 수의 수를 표시 됩니다. `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 더 높은 `dcount` 값의 전체 시퀀스를 완료하는 데 더 많은 시간이 주어지는 것을 볼 수 있습니다(깔때기의 단계에 도달하는 시퀀스의 발생이 더 많다는 것을 의미).
