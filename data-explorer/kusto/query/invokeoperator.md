---
title: invoke 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 invoke 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1aca8cda34e1ee8506d5be6633cfd46fd912c6c3
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271522"
---
# <a name="invoke-operator"></a>invoke 연산자

As 테이블 형식 매개 변수 인수로 원본을 수신 하는 람다를 호출 `invoke` 합니다.

```kusto
T | invoke foo(param1, param2)
```

**구문**

`T | invoke`*함수* `(` [*param1* `,` *param2*]`)`

**인수**

* *T*: 테이블 형식 원본입니다.
* *function*: 평가할 람다 식 또는 함수 이름의 이름입니다.
* *param1*, *param2* ...: 추가 람다 인수입니다.

**반환**

계산 된 식의 결과를 반환 합니다.

**참고 사항**

테이블 형식 인수를 사용할 수 있는 람다 식을 선언 하는 방법에 대 한 자세한 내용은 [let 문](./letstatement.md) 을 참조 하세요.

**예제**

다음 예제에서는 연산자를 사용 하 여 람다 식을 호출 하는 방법을 보여 줍니다 `invoke` .

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
