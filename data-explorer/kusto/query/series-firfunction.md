---
title: series_fir() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fir()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: bbdf93504be431d77c19a881cf79d1e1d3d400ac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663569"
---
# <a name="series_fir"></a>series_fir()

계열에 유한 임펄스 응답 필터를 적용합니다.  

동적 숫자 배열을 포함하는 식을 입력으로 가져와 [유한 임펄스 응답](https://en.wikipedia.org/wiki/Finite_impulse_response) 필터를 적용합니다. `filter` 계수를 지정하여 이동 평균, 스무딩, 변경 감지 및 더 많은 사용 사례를 계산하는 데 사용할 수 있습니다. 이 함수는 필터 계수의 정적 동적 배열 및 동적 배열을 포함하는 열을 입력으로 사용하고 열에 해당 필터를 적용합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다.  

**구문**

`series_fir(`*x* `,` *filter* 필터`,` [ *정규화*`,` *[가운데]]*`)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀로, 일반적으로 [메이크 계열](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *필터*: 필터의 계수를 포함하는 상수 식(숫자 값의 동적 배열로 저장됨).
* *정규화*: 필터를 정규화해야 하는지 여부를 나타내는 선택적 부울 값(예: 계수의 합으로 나눈 값). *필터에* 음수 값이 포함된 경우 `false` *정규화는* `null`로 지정되어야 합니다. 지정하지 않으면 *필터에* 음수 값이 있는 경우 *필터에* *filter*음수 값이 하나 이상 포함되어 있으면 *정규화가* `false`가정됩니다.  
정규화는 계수의 합이 1이므로 필터가 계열을 증폭하거나 감쇠하지 않도록 하는 편리한 방법입니다. 예를 들어 *4개의*저장소이동 평균을 필터 =[1,1,1,1]로 지정하고 *정규화된*=true로 지정할 수 있으며 이는 [0.25,0.25.0.25,0.25]를 입력하는 것보다 쉽습니다.
* *중심*: 필터가 현재 점 전후의 시간 창또는 현재 지점의 시간 창에 대칭으로 적용되는지 여부를 나타내는 선택적 부울 값입니다. 기본적으로 center는 데이터 스트리밍 시나리오에 적합한 false입니다. 이 경우에는 현재 및 이전 시점에만 필터를 적용할 수 있습니다. 그렇지만 임시 처리를 위해 center를 true로 설정하여 시계열과 동기화된 상태를 유지할 수 있습니다(아래 예제 참조). 기술적으로 이 매개 변수는 필터의 [그룹 지연을](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)제어합니다.

**예**

* 이동 평균을 5점 으로 계산하면 *필터*=[1,1,1,1,1]] 및 정규화(기본값)를 *normalize* = `true` 설정하여 수행할 수 있습니다. 중심(기본값) *center* = `false` 대: `true`

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

이 쿼리는 다음을 반환합니다.  
*5h_MovingAvg*: 5 점 이동 평균 필터. 스파이크는 다듬어지고 피크는 (5-1)/2 = 2h로 전환됩니다.  
*5h_MovingAvg_centered*: 동일하지만 설정 중심 = true로 피크가 원래 위치에 유지됩니다.

:::image type="content" source="images/samples/series-fir.png" alt-text="시리즈 전나무":::

* *필터*=[1,-1]을 설정하여 점과 이전 점간의 차이를 계산할 수 있습니다.

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```
:::image type="content" source="images/samples/series-fir2.png" alt-text="시리즈 전나무 2":::
