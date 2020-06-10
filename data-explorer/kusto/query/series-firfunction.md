---
title: series_fir ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fir ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 616fee7b0a1b6852f66d3db22846b2645e03135f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665013"
---
# <a name="series_fir"></a>series_fir()

계열의 전나무 (유한 임펄스 Response) 필터를 적용 합니다.  

함수는 동적 숫자 배열을 입력으로 포함 하는 식을 사용 하 여 [유한 임펄스 Response](https://en.wikipedia.org/wiki/Finite_impulse_response) 필터를 적용 합니다. 계수를 지정 하 여 `filter` 이동 평균, 다듬기, 변경 검색 및 많은 사용 사례를 계산 하는 데 사용할 수 있습니다. 함수는 동적 배열과 필터 계수의 정적 동적 배열을 포함 하는 열을 입력으로 사용 하 고 열에 필터를 적용 합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다.  

**구문**

`series_fir(`*x* `,` *필터* [ `,` *정규화*[ `,` *센터*]]`)`

**인수**

* *x*: 숫자 값의 동적 배열 셀입니다. 일반적으로 [series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *filter*: 필터의 계수를 포함 하는 상수 식입니다 (숫자 값의 동적 배열로 저장 됨).
* *정규화*: 필터를 정규화 해야 하는지 여부를 나타내는 선택적 부울 값입니다. 즉, 계수의 합계로 나눕니다. 필터에 음수 값이 포함 되어 있으면 *정규화* 를로 지정 해야 합니다 `false` . 그렇지 않으면 결과는가 됩니다 `null` . 이 값을 지정 하지 않으면 *필터*에 음수 값이 있는지 여부에 따라 기본값 *정규화* 가 가정 됩니다. *필터* 에 하나 이상의 음수 값이 포함 된 경우 *정규화* 는로 가정 됩니다 `false` .  
정규화는 계수의 합계가 1 인지 확인 하는 편리한 방법입니다. 그런 다음 필터는 계열을 강화 하거나 경우 하지 않습니다. 예를 들어 4 개의 bin의 이동 평균은 *filter*= [1, 1, 1, 1] 및 *정규화*됨 = true로 지정할 수 있으며이는 [0.25, 0.25.0.25, 0.25]를 입력 하는 것 보다 더 쉽습니다.
* *center*: 필터가 현재 지점 전후에 대칭으로 적용 되는지 아니면 현재 지점에서 역순으로 적용 되는지 여부를 나타내는 선택적 부울 값입니다. 기본적으로 center는 false로, 스트리밍 데이터의 시나리오에 적합 합니다. 여기서는 현재 및 이전 지점 에서만 필터를 적용할 수 있습니다. 그러나 임시 처리의 경우이를로 설정 하 여 `true` 시계열과 동기화 된 상태로 유지할 수 있습니다. 아래 예제를 참조하세요. 이 매개 변수는 필터의 [그룹 지연을](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)제어 합니다.

**예**

* *Filter*= [1, 1, 1, 1, 1] 및 *정규화* = (기본값)를 설정 하 여 5 개 점의 이동 평균을 계산 `true` 합니다. *Center* = `false` (기본값) 및 `true` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

이 쿼리는 다음을 반환합니다.  
*5h_MovingAvg*: 5 개의 점이 이동 평균 필터입니다. 스파이크는 다듬어지고 피크는 (5-1)/2 = 2h로 전환됩니다.  
*5h_MovingAvg_centered*: 동일 하지만를 설정 하 여 `center=true` 최대가 원래 위치에 유지 됩니다.

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="시리즈 전나무" border="false":::

* 지점과 그 이전 사이의 차이를 계산 하려면 *filter*= [1,-1]을 설정 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="시리즈 전나무 2" border="false":::
