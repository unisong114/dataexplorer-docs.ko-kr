---
title: series_greater ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_greater ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7181e703ff6085d463f80bd169ae064066031d66
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372648"
---
# <a name="series_greater"></a>series_greater()

`>`두 숫자 계열 입력의 요소 수준 더 큰 () 논리 연산을 계산 합니다.

**구문**

`series_greater (`*Series1* `,` *Series2*`)`

**인수**

* *Series1, Series2*: 요소를 비교 하는 데 사용할 숫자 배열을 입력 합니다. 모든 인수는 동적 배열 이어야 합니다. 

**반환**

두 입력 간의 계산 된 요소 보다 큰 논리 연산을 포함 하는 부울의 동적 배열입니다. 숫자가 아닌 요소나 비 기존 요소 (크기가 다른 배열)는 `null` 요소 값을 생성 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, false, true]|

**참고 항목**

전체 계열 통계 비교는 다음을 참조 하세요.
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
