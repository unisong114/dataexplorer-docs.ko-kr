---
title: series_seasonal ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_seasonal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 054d4be758001609fbc3100a4a6c8698ef8f69f6
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717311"
---
# <a name="series_seasonal"></a>series_seasonal()

검색 되거나 지정 된 계절 기간에 따라 계열의 계절 구성 요소를 계산 합니다.

**구문**

`series_seasonal(`*계열* `[,` *기간*`])`

**인수**

* *계열*: 입력 숫자 동적 배열
* *period* (선택 사항): 각 계절 기간에 있는 Bin의 정수 수, 가능한 값:
    *  -1 (기본값): *0.7*의 임계값과 함께 [series_periods_detect ()](series-periods-detectfunction.md) 를 사용 하 여 기간을 자동으로 검색 합니다. 계절성가 검색 되지 않으면 0을 반환 합니다.
    * 양의 정수: 계절 구성 요소에 대 한 기간으로 사용 됩니다.
    * 다른 모든 값: 계절성를 무시 하 고 일련의 0을 반환 합니다.

**반환**

계열의 계산 된 계절 구성 요소가 포함 된 *계열* 입력과 동일한 길이의 동적 배열입니다. 계절 구성 요소는 전체 기간의 bin 위치에 해당 하는 모든 값의 *중앙값* 으로 계산 됩니다.

## <a name="examples"></a>예

### <a name="auto-detect-the-period"></a>기간 자동 검색

다음 예에서는 시리즈의 기간이 자동으로 검색 됩니다. 첫 번째 시리즈의 기간은 6 개의 bin 및 두 번째 5 개의 계급로 검색 됩니다. 세 번째 계열의 기간이 너무 짧아서 검색할 수 없으며 일련의 0이 반환 됩니다. [마침표를 적용 하는 방법](#force-a-period)에 대 한 다음 예제를 참조 하세요.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2, 5, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1]|[1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0]|
|[8, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10]|[10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]|

### <a name="force-a-period"></a>마침표 강제 적용

이 예제에서는 계열의 기간이 너무 짧아서 [series_periods_detect ()](series-periods-detectfunction.md)에서 검색할 수 없기 때문에 주기적으로 기간을 강제로 적용 하 여 계절 패턴을 가져옵니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1, 3, 5, 1, 3, 5, 2, 4, 6]|[1.0, 3.0, 5.0, 1.0, 3.0, 5.0, 1.0, 3.0, 5.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5]|
 
## <a name="next-steps"></a>다음 단계

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
