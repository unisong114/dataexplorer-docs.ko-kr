---
title: sequence_detect 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 sequence_detect 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7b4b3d2b43bea2eeb96c9bbca94131cb7887db8c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345694"
---
# <a name="sequence_detect-plugin"></a>sequence_detect 플러그 인

제공 된 조건자에 따라 시퀀스 발생을 검색 합니다.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `sequence_detect` `(` *TimelineColumn* `,` *MaxSequenceStepWindow* `,` *MaxSequenceSpan* `,` *Expr1* `,` *Expr2* `,` ..., *Dim1* `,` *Dim2* `,` ...`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *TimelineColumn*: 타임 라인을 나타내는 열 참조는 소스 식에 있어야 합니다.
* *MaxSequenceStepWindow*: 시퀀스의 두 순차 단계 사이에서 허용 되는 최대 timespan의 스칼라 상수 값입니다.
* *MaxSequenceSpan*: 모든 단계를 완료 하기 위한 시퀀스의 최대 범위에 대 한 스칼라 상수 값입니다.
* *Expr1*, *Expr2*, ...: 시퀀스 단계를 정의 하는 부울 조건자 식
* *Dim1*, *Dim2*, ...: 시퀀스를 상호 연결 하는 데 사용 되는 차원 식

## <a name="returns"></a>반환

테이블의 각 행이 단일 시퀀스 발생을 나타내는 단일 테이블을 반환 합니다.

* *Dim1*, *Dim2*, ...: 시퀀스를 상호 연결 하는 데 사용 된 차원 열입니다.
* *Expr1*_*TimelineColumn*, *Expr2*_*TimelineColumn*, ...: 시간 값이 있는 열은 각 시퀀스 단계의 타임 라인을 나타냅니다.
* *기간*: 전체 시퀀스 시간 창

## <a name="examples"></a>예제

### <a name="exploring-storm-events"></a>스톰 이벤트 탐색 

다음 쿼리는 테이블 StormEvents (2007에 대 한 날씨 통계)를 확인 하 고, ' 과도 한 열 '의 시퀀스 다음에 5 일 이내에 ' 산 '가 오는 경우를 보여 줍니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|남부 노스다코타|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|: 00:00|
|텍사스|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|캘리포니아|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|캘리포니아|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|캘리포니아|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|
