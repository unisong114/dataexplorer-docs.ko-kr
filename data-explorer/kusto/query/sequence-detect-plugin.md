---
title: sequence_detect 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 sequence_detect 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afe4b41cc9bf3e81389edfb1fac76472772fa8f6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509183"
---
# <a name="sequence_detect-plugin"></a>sequence_detect 플러그인

제공된 조건어를 기반으로 시퀀스 발생을 검색합니다.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**구문**

*T* `| evaluate` `,` `,` `,` `,` `,` *Dim2* *TimelineColumn* `,` *Dim1* *MaxSequenceStepWindow* *Expr2* *Expr1* *MaxSequenceSpan* 타임라인열`,` MaxSequenceStepWindow MaxSequenceSpan Expr1 Expr2 ..., Dim1 Dim2 ... `sequence_detect` `(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *TimelineColumn*: 타임라인을 나타내는 열 참조가 소스 식에 있어야 합니다.
* *MaxSequenceStepWindow*: 시퀀스의 2개의 순차적 단계 사이의 최대 허용 시간 범위의 스칼라 상수 값
* *MaxSequenceSpan*: 시퀀스가 모든 단계를 완료하는 최대 범위의 스칼라 상수 값
* *Expr1*, *Expr2*, ...: 부울 술어 표현식 정의 시퀀스 단계
* *Dim1*, *Dim2*, ...: 시퀀스의 상관 관계를 지정하는 데 사용되는 차원 표현식

**반환**

테이블의 각 행이 단일 시퀀스 발생을 나타내는 단일 테이블을 반환합니다.

* *Dim1*, *Dim2*, ...: 시퀀스의 상관 관계를 지정하는 데 사용된 차원 열입니다.
* *Expr1*_*타임라인열*, *Expr2*_*타임라인열*, ...: 각 시퀀스 단계의 타임라인을 나타내는 시간 값이 있는 열입니다.
* *지속 시간*: 전체 시퀀스 시간 창

**예**

### <a name="exploring-storm-events"></a>폭풍 이벤트 탐색 

다음 쿼리는 StormEvents(2007년 의 날씨 통계)를 살펴보고 5일 이내에 '과도한 열'이 이어지고 '산불'이 발생한 경우를 보여 주며, 그 다음에 '산불'이 발생한 경우를 보여 주며, '과도한 열'의 순서를 보여 주며, 그 다음에 '산불'이 발생한 경우를 보여 주어 있습니다.

```kusto
StormEvents
| evaluate sequence_detect(
        StartTime,
        5d,  // step max-time
        5d,  // sequence max-time
        heat=(EventType == "Excessive Heat"), 
        wildfire=(EventType == 'Wildfire'), 
        State)
```

|시스템 상태|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|캘리포니아|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|캘리포니아|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|캘리포니아|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|사우스다코타 주|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|텍사스|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|캘리포니아|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|캘리포니아|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|캘리포니아|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|