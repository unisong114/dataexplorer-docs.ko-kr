---
title: 연산자 호출 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 호출 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513705"
---
# <a name="invoke-operator"></a>invoke 연산자

테이블 형식 매개 변수 인수의 `invoke` 소스를 수신 하는 람다 호출합니다.

```kusto
T | invoke foo(param1, param2)
```

**구문**

`T | invoke`*기능*`(`[*param1* `,` *param2*]`)`

**인수**

* *T*: 테이블 형식 소스입니다.
* *기능*: 평가할 람다 식 또는 함수 이름의 이름입니다.
* *param1*, *param2* ... : 추가 람다 인수.

**반환**

평가된 식의 결과를 반환합니다.

**참고 사항**

테이블 형식 인수를 허용할 수 있는 lambda 식을 선언하는 방법에 대한 자세한 내용은 [let 문을](./letstatement.md) 참조하십시오.

**예제**

다음 예제에서는 연산자 사용 하 여 `invoke` lambda 식을 호출 하는 방법을 보여 주어 있습니다.

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
