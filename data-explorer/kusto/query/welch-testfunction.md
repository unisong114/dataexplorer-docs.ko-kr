---
title: welch_test() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 welch_test()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9f4b3d86bf3d679fd4fdd320b394956c71d3e97
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504491"
---
# <a name="welch_test"></a>welch_test()

[웰치 테스트 함수의](https://en.wikipedia.org/wiki/Welch%27s_t-test) p_value 계산

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

**구문**

`welch_test(`*mean1*`, `*분산1 카운트1*`, `*count1*`, `*평균2*`, `*분산2*`, `*카운트2*`)`

**인수**

* *mean1*: 첫 번째 계열의 평균(평균) 값을 나타내는 표현식
* *분산1*: 첫 번째 계열의 분산 값을 나타내는 표현식
* *count1*: 첫 번째 계열의 값 수를 나타내는 표현식
* *mean2*: 2번째 계열의 평균(평균) 값을 나타내는 표현식
* *분산2*: 2번째 계열의 분산 값을 나타내는 표현식
* *count2*: 2시리즈의 값 수를 나타내는 표현식

**반환**

[위키백과에서](https://en.wikipedia.org/wiki/Welch%27s_t-test):

통계에서 Welch의 t 검정 또는 불평등 한 분산 t-test는 두 모집단이 동일한 수단을 가지고 있다는 가설을 테스트하는 데 사용되는 두 표본 위치 테스트입니다. 웰치의 t-시험은 학생의 t-검정, 즉 학생의 t-검정을 통해 파생되었으며 두 표본의 차이가 불평등하고 표본 크기가 같지 않을 때 더 신뢰할 수 있습니다. 이러한 테스트는 일반적으로 비교되는 두 샘플의 기본 통계 단위가 겹치지 않을 때 적용되기 때문에 "페어링되지 않은" 또는 "독립적인 샘플" t 검정이라고합니다. 웰치의 t-시험은 학생의 t-테스트보다 덜 인기가 있고 독자들에게 덜 익숙할 수 있다는 점을 감안할 때, 더 유익한 이름은 "웰치의 불평등 한 차이 t-테스트"또는 "불평등 분산 t-테스트"입니다.