---
title: welch_test ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 welch_test ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cc0cc7ca77efeae583adeaf7a983d18498e5af96
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338272"
---
# <a name="welch_test"></a>welch_test()

[Welch 함수의](https://en.wikipedia.org/wiki/Welch%27s_t-test) p_value 계산 합니다.

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

## <a name="syntax"></a>Syntax

`welch_test(`*mean1* `, ` *variance1* `, ` *count1* `, ` *mean2* `, ` *variance2* `, ` *count2*`)`

## <a name="arguments"></a>인수

* *mean1*: 첫 번째 계열의 평균 (평균) 값을 나타내는 식입니다.
* *variance1*: 첫 번째 계열의 분산 값을 나타내는 식입니다.
* *count1*: 첫 번째 계열의 값 수를 나타내는 식입니다.
* *mean2*: 두 번째 계열의 평균 (평균) 값을 나타내는 식입니다.
* *variance2*: 두 번째 계열의 분산 값을 나타내는 식입니다.
* *count2*: 두 번째 계열의 값 수를 나타내는 식입니다.

## <a name="returns"></a>반환

[위키백과](https://en.wikipedia.org/wiki/Welch%27s_t-test)에서:

통계에서 Welch의 t-테스트는 두 모집단의 의미가 동일한 가설을 테스트 하는 데 사용 되는 2 표본 위치 테스트입니다. Welch의 t-테스트는 학생의 t 테스트를 조정 하는 것으로, 두 샘플의 분산이 동일 하지 않으며 샘플 크기가 같지 않은 경우 더 안정적입니다. 이러한 테스트를 종종 "페어링되지 않음" 또는 "독립적인 샘플" t-테스트 라고 합니다. 테스트는 일반적으로 비교할 두 샘플의 기본 통계 단위가 겹치지 않는 경우에 적용 됩니다. Welch의 t-테스트는 학생의 t 테스트 보다 인기가 적고 독자에 게 더 친숙 하지 않을 수 있습니다. 테스트를 "Welch의 동일 하지 않은 분산 t-테스트" 또는 "같지 않음 분산 t-테스트" 라고도 합니다.
