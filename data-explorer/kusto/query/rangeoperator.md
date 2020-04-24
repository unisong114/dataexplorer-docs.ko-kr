---
title: 범위 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 범위 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510611"
---
# <a name="range-operator"></a>range 연산자

단일 열로 이루어진 값 테이블을 생성합니다.

참고로 파이프라인 입력이 없습니다. 

**구문**

`range`*columnName* `from` *시작* `to` *중지* `step` *단계*

**인수**

* *columnName*: 출력 테이블의 단일 열 이름입니다.
* *시작*: 출력에서 가장 작은 값입니다.
* *stop*: 출력에서 생성되는 가장 높은 값(또는 이 *값을* 단계별로 단계별로 단계인 경우 가장 높은 값에 바인딩됨)입니다.
* *단계*: 두 연속 값의 차이입니다. 

인수는 숫자, 날짜 또는 시간 간격 값이어야 합니다. 아무 테이블의 열이나 참조할 수는 없습니다. 입력 테이블을 기반으로 범위를 계산하려면 mv 확장 연산자로 범위 함수를 사용합니다. 

**반환**

*columnName이라는*단일 열이있는 테이블이름, 그 값이 *시작되고* *시작* `+` *단계는*... 최대 및 *중지할*때까지 .

**예제**  

지난 7일 자정에 대한 테이블입니다. bin(floor) 함수는 각 시간을 날짜의 시작으로 줄입니다.

```kusto
range LastWeek from ago(7d) to now() step 1d
```

|전주|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


`Steps`라는 단일 열을 가진 테이블이며, 해당 열의 형식은 `long`, 해당 열의 값은 `1`, `4`, `7`입니다.

```kusto
range Steps from 1 to 8 step 3
```

다음 예제에서는 연산자가 `range` 작은 임시 차원 테이블을 만드는 데 사용할 수 있는 방법을 보여 주며, 이 테이블은 원본 데이터에 값이 없는 0을 도입하는 데 사용됩니다.

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```