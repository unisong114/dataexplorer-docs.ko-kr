---
title: series_not_equals() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_not_equals()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ce9c695ececf1ac9f1fbe783ebe0fa35986f3d0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508180"
---
# <a name="series_not_equals"></a>series_not_equals()

두 개의 숫자 계열 입력의 요소 별 ()`!=`논리 연산을 계산합니다.

**구문**

`series_not_equals (`*시리즈1* `,` *시리즈2*`)`

**인수**

* *Series1, Series2*: 입력 숫자 배열은 요소별 비교입니다. 모든 인수는 동적 배열이어야 합니다. 

**반환**

계산된 요소 현명을 포함하는 부울의 동적 배열은 두 입력 간에 균등하지 않은 논리 연산을 포함합니다. 숫자가 아닌 요소 또는 존재하지 않는 요소(다른 크기의 배열)는 `null` 요소 값을 생성합니다.

**예제**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[참, 거짓, 참]|

**참고 항목**

전체 계열 통계 비교는 다음을 참조하십시오.
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)