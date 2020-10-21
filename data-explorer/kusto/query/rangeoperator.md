---
title: 범위 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 범위 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1eddf7fe1e9121a134e222f6a19b9ebeede3a762
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243140"
---
# <a name="range-operator"></a>range 연산자

단일 열로 이루어진 값 테이블을 생성합니다.

참고로 파이프라인 입력이 없습니다. 

## <a name="syntax"></a>구문

`range`*columnName* `from` *시작* `to` *중지* `step` *단계*

## <a name="arguments"></a>인수

* *columnName*: 출력 테이블의 단일 열 이름입니다.
* *start*: 출력에서 가장 작은 값입니다.
* *stop*: 출력에서 가장 높은 값이 생성 되는 값입니다 .이 값에 대 한 단계 단계를 수행 하 *는 경우에* 는 가장 높은 값에 바인딩되어 있습니다.
* *step*: 연속 된 두 값 간의 차이입니다. 

인수는 숫자, 날짜 또는 시간 간격 값이어야 합니다. 아무 테이블의 열이나 참조할 수는 없습니다. 입력 테이블을 기반으로 범위를 계산 하려면 범위 함수를 사용 합니다 .이 함수는 mv 확장 연산자와 함께 사용 됩니다. 

## <a name="returns"></a>반환

값이 *start*, *start* *columnName* `+` *step*, ... 인 columnName 이라는 단일 열이 있는 테이블입니다. *중지할*때까지

## <a name="example"></a>예제  

지난 7일 자정에 대한 테이블입니다. bin(floor) 함수는 각 시간을 날짜의 시작으로 줄입니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

다음 예에서는 연산자를 사용 하 여 `range` 작은 임시 차원 테이블을 만든 다음 원본 데이터에 값이 없는 0을 도입 하는 방법을 보여 줍니다.

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
