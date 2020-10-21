---
title: rolling_percentile 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 rolling_percentile 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f64a7e5c183e34e81781986d5c28f189a04291e7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242887"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile () 플러그 인

*ValueColumn* 모집단의 지정 된 백분위 수에 대 한 예상 값을 *binsize*당 롤링 (슬라이딩) *binsperwindow* 크기 창에 반환 합니다.

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

## <a name="syntax"></a>구문

*T* `| evaluate` `rolling_percentile(` *ValueColumn* `,` *백분위* 수 `,` *indexcolumn* `,` *binsize* `,` *binsperwindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *ValueColumn*: 백분위 수를 계산 하는 값이 포함 된 열의 이름입니다. 
* *백분위*수: 계산할 백분위 수가 포함 된 스칼라입니다.
* *Indexcolumn*: 롤링 창을 실행할 열의 이름입니다.
* *Binsize*: *indexcolumn*위에 적용할 bin의 크기를 가진 스칼라입니다.
* *Binsperwindow*: 각 창에 포함 된 bin 수가 포함 된 스칼라입니다.
* *dim1*, *dim2*, ...: (선택 사항) 분할할 차원 열의 목록입니다.

## <a name="returns"></a>반환

각 bin (지정 된 경우) 마다 행이 있는 테이블을 반환 합니다 .이 값의 값은 bin (포함)으로 끝납니다. 고유 카운트 값, 새 값의 고유 개수, 각 기간에 대 한 집계 된 고유 카운트.

출력 테이블 스키마는 다음과 같습니다.


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


## <a name="examples"></a>예

### <a name="rolling-3-day-median-value-per-day"></a>롤링 3-일 중앙값 값 

다음 쿼리는 일일 세분성의 3 일 중앙값을 계산 합니다. 출력의 각 행은 bin 자체를 포함 하 여 마지막 3 개의 bin (일)에 대 한 중앙값을 나타냅니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>차원에의 한 일별 롤링 3 일 중앙값

위의 예제와 동일 하지만 이제 차원의 각 값에 대해 분할 된 롤링 창을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|타임스탬프| EvenOrOdd|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   에도|   12|
|2018-01-02 00:00:00.0000000|   에도|   24|
|2018-01-03 00:00:00.0000000|   에도|   36|
|2018-01-04 00:00:00.0000000|   에도|   60|
|2018-01-05 00:00:00.0000000|   에도|   84|
|2018-01-06 00:00:00.0000000|   에도|   108|
|2018-01-07 00:00:00.0000000|   에도|   132|
|2018-01-08 00:00:00.0000000|   에도|   156|
|2018-01-09 00:00:00.0000000|   에도|   180|
|2018-01-10 00:00:00.0000000|   에도|   204|
|2018-01-01 00:00:00.0000000|   홀수|    11|
|2018-01-02 00:00:00.0000000|   홀수|    23|
|2018-01-03 00:00:00.0000000|   홀수|    35|
|2018-01-04 00:00:00.0000000|   홀수|    59|
|2018-01-05 00:00:00.0000000|   홀수|    83|
|2018-01-06 00:00:00.0000000|   홀수|    107|
|2018-01-07 00:00:00.0000000|   홀수|    131|
|2018-01-08 00:00:00.0000000|   홀수|    155|
|2018-01-09 00:00:00.0000000|   홀수|    179|
|2018-01-10 00:00:00.0000000|   홀수|    203|
