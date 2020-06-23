---
title: toscalar ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 toscalar ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15d9056ec21eb6f25ccbc985d659f310d670f02d
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133416"
---
# <a name="toscalar"></a>toscalar()

계산 된 식의 스칼라 상수 값을 반환 합니다. 

이 함수는 준비 된 계산이 필요한 쿼리에 유용 합니다. 예를 들어 총 이벤트 수를 계산한 다음 결과를 사용 하 여 모든 이벤트의 특정 비율을 초과 하는 그룹을 필터링 합니다.

**구문**

`toscalar(`*식*`)`

**인수**

* *Expression*: 스칼라 변환에 대해 평가 되는 식입니다.

**반환**

계산 된 식의 스칼라 상수 값입니다.
결과가 테이블 형식 이면 첫 번째 열과 첫 번째 행이 변환을 위해 사용 됩니다.

> [!TIP]
> 을 사용 하는 경우 쿼리 가독성을 위해 [let 문을](letstatement.md) 사용할 수 있습니다 `toscalar()` .

**참고 사항**

`toscalar()`쿼리 실행 중에 일정 한 횟수 만큼 계산할 수 있습니다.
`toscalar()`행 수준 (각 행에 대 한 시나리오)에는 함수를 적용할 수 없습니다.

**예**

`Start`, `End` 및을 `Step` 스칼라 상수로 평가 하 고 평가에 결과를 사용 `range` 합니다.

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
