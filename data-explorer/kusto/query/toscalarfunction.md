---
title: 토스칼라() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 toscalar()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505902"
---
# <a name="toscalar"></a>toscalar()

평가된 식의 스칼라 상수 값을 반환합니다. 

이 함수는 이벤트의 총 수를 계산한 다음 모든 이벤트의 특정 백분율을 초과하는 필터링 그룹에 사용하는 등 단계별 계산이 필요한 쿼리에 유용합니다. 

**구문**

`toscalar(`*식*`)`

**인수**

* *표현식*: 스칼라 변환에 대해 평가되는 표현식  

**반환**

평가된 식의 스칼라 상수 값입니다.
식 결과가 표형식인 경우 변환을 위해 첫 번째 열과 첫 번째 행이 수행됩니다.

> [!TIP]
> 을 사용할 때 [쿼리의](letstatement.md) 가독성을 위해 `toscalar()`let 문을 사용할 수 있습니다.

**참고 사항**

`toscalar()`쿼리 실행 중에 일정한 횟수를 계산할 수 있습니다.
즉, `toscalar()` 함수는 행 수준(각 행에 대한 시나리오)에 적용할 수 없습니다.

**예**

다음 쿼리는 `Start`스칼라 `End` `Step` 상수로 평가하고 평가에 `range` 사용합니다. 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|단계|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|