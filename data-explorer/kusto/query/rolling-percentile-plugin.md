---
title: rolling_percentile 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 rolling_percentile 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 02def4069c83eeec080ca059493132619fce30d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510271"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile 플러그인

BinSize당 롤링(슬라이딩) *BinsPerWindow* 크기 창에서 *ValueColumn* 모집단의 지정된 백분위수에 대한 예상값을 *반환합니다.*

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**구문**

*T* `| evaluate` `,` `,` `,` `,` *ValueColumn* `,` `,` `,` *dim2* *BinSize* *dim1* *BinsPerWindow* *IndexColumn* *Percentile* ValueColumn 백분위수 인덱스열 BinSize BinsPerWindow [ dim1 dim2 ...] `rolling_percentile(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *ValueColumn*: 백분위수를 계산하는 값이 있는 열의 이름입니다. 
* *백분위수*: 계산할 백분위수와 스칼라.
* *IndexColumn*: 롤링 창을 실행할 열의 이름입니다.
* *BinSize*: *인덱스 열*위에 적용할 저장소 크기의 스칼라.
* *BinsPerWindow*: 각 창에 포함 된 빈의 수와 스칼라.
* *dim1*, *dim2*, ... : (선택 사항) 크기 열의 목록으로 슬라이스합니다.

**반환**

창에서 롤링 백분위수(포함)가 있는 각 bin당 행(및 지정된 경우 차원조합)이 있는 테이블을 반환합니다. 고유 개 수 값, 새 값의 고유 개수, 각 시간 창에 대해 집계된 고유 개수입니다.

출력 테이블 스키마는 다음과 같은 것입니다.


|인덱스 열|어둡게 1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**예**

### <a name="rolling-3-day-median-value-per-day"></a>하루 3일 중앙값 롤링 

다음 쿼리는 일별 세분성으로 3일 중앙값을 계산합니다. 출력의 각 행은 Bin 자체를 포함하여 마지막 3개의 저장소(일)에 대한 중앙값을 나타냅니다.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3)
```

|타임스탬프|rolling_3_percentile_val_50|
|---|---|
|2018-01-01 00:00:00.0000000|   12|
|2018-01-02 00:00:00.0000000|   24|
|2018-01-03 00:00:00.0000000|   36|
|2018-01-04 00:00:00.0000000|   60|
|2018-01-05 00:00:00.0000000|   84|
|2018-01-06 00:00:00.0000000|   108|
|2018-01-07 00:00:00.0000000|   132|
|2018-01-08 00:00:00.0000000|   156|
|2018-01-09 00:00:00.0000000|   180|
|2018-01-10 00:00:00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>차원별로 일일 3일 중앙값 롤링

위의 동일한 예이지만 이제 차원의 각 값에 대해 분할된 롤링 창도 계산합니다.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|타임스탬프| 이븐오로드|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   도|   12|
|2018-01-02 00:00:00.0000000|   도|   24|
|2018-01-03 00:00:00.0000000|   도|   36|
|2018-01-04 00:00:00.0000000|   도|   60|
|2018-01-05 00:00:00.0000000|   도|   84|
|2018-01-06 00:00:00.0000000|   도|   108|
|2018-01-07 00:00:00.0000000|   도|   132|
|2018-01-08 00:00:00.0000000|   도|   156|
|2018-01-09 00:00:00.0000000|   도|   180|
|2018-01-10 00:00:00.0000000|   도|   204|
|2018-01-01 00:00:00.0000000|   이상한|    11|
|2018-01-02 00:00:00.0000000|   이상한|    23|
|2018-01-03 00:00:00.0000000|   이상한|    35|
|2018-01-04 00:00:00.0000000|   이상한|    59|
|2018-01-05 00:00:00.0000000|   이상한|    83|
|2018-01-06 00:00:00.0000000|   이상한|    107|
|2018-01-07 00:00:00.0000000|   이상한|    131|
|2018-01-08 00:00:00.0000000|   이상한|    155|
|2018-01-09 00:00:00.0000000|   이상한|    179|
|2018-01-10 00:00:00.0000000|   이상한|    203|