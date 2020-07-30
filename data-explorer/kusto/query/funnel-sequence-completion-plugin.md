---
title: funnel_sequence_completion 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 funnel_sequence_completion 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 3511d15ebf0f5e3708deeeed981a8a6808da2e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347938"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion 플러그 인

서로 다른 기간을 비교 하 여 완료 된 시퀀스 단계의 깔때기를 계산 합니다.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `funnel_sequence_completion(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Step* `,` *StateColumn* `,` *Sequence* `,` *MaxSequenceStepWindows*`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *IdColum*: 열 참조는 원본 식에 있어야 합니다.
* *TimelineColumn*: 타임 라인을 나타내는 열 참조는 원본 식에 있어야 합니다.
* *Start*: 분석 시작 기간의 스칼라 상수 값입니다.
* *끝*: 분석 종료 기간의 스칼라 상수 값입니다.
* *단계*: 분석 단계 기간 (bin)의 스칼라 상수 값입니다.
* *StateColumn*: 상태를 나타내는 열 참조는 원본 식에 있어야 합니다.
* *Sequence*: 시퀀스 값이 포함 된 상수 동적 배열입니다 (값은에서 조회 됨 `StateColumn` ).
* *MaxSequenceStepWindows*: 시퀀스의 첫 번째 및 마지막 순차 단계 사이에 허용 되는 최대 timespan 값이 포함 된 스칼라 상수 동적 배열입니다. 배열의 각 창 (기간)은 깔때기 분석 결과를 생성 합니다.

## <a name="returns"></a>반환

분석 된 시퀀스에 대해 깔때기 다이어그램을 생성 하는 데 유용한 단일 테이블을 반환 합니다.

* `TimelineColumn`: 분석 된 시간 창
* `StateColumn`: 시퀀스의 상태입니다.
* `Period`: 깔때기형 시퀀스의 단계를 완료 하는 데 허용 되는 최대 기간 (기간)으로 시퀀스의 첫 번째 단계에서 측정 됩니다. *MaxSequenceStepWindows* 의 각 값은 별도의 기간을 사용 하 여 깔때기형 분석을 생성 합니다. 
* `dcount`: `IdColumn` 첫 번째 시퀀스 상태에서 값으로 전환 된의 고유 카운트 기간입니다 `StateColumn` .

## <a name="examples"></a>예제

### <a name="exploring-storm-events"></a>스톰 이벤트 탐색 

다음 쿼리는 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` "전체" 시간 (1 시간, timegenerated>now-4hours, 1 일)에서 시퀀스의 완료 깔때기를 확인 합니다. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|`StartTime`|`EventType`|`Period`|`dcount`|
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
결과는 3 funnels (기간: 1 시간, 4 시간, 1 일)입니다. 각 깔때기형 단계에 대해 여러 개의 고유 개수가 표시 됩니다. 의 전체 시퀀스를 완료 하는 데 더 많은 시간이 지정 되 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` 면 더 높은 값을 `dcount` 가져옵니다. 즉, 깔때기형 단계에 도달 하는 시퀀스가 더 많이 있습니다.
