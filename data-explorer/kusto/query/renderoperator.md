---
title: 렌더 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 렌더링 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 645860405a165f3304f655e42d2ced9b8777b8d0
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765481"
---
# <a name="render-operator"></a>render 연산자

사용자 에이전트에게 특정 방식으로 쿼리 결과를 렌더링하도록 지시합니다.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * 렌더 연산자는 쿼리의 마지막 연산자여야 하며 단일 테이블 형식 데이터 스트림 결과를 생성하는 쿼리에서만 사용해야 합니다.
> * 렌더 연산자는 데이터를 수정하지 않습니다. 결과의 확장 속성에 추가("시각화")를 삽입합니다. 이 어구에는 쿼리에서 연산자가 제공한 정보가 포함됩니다.
> * 시각화 정보의 해석은 사용자 에이전트에 의해 수행됩니다. 다른 에이전트(예: Kusto.Explorer, Kusto.WebExplorer)는 서로 다른 시각화를 지원할 수 있습니다.

**구문**

*T* `|` `with` `(` *PropertyName* `=` *PropertyValue* `,` *Visualization* 시각화 [ 속성 이름 속성 값 [...] `render` `)`]

위치:

* *시각화는* 사용할 시각화의 종류를 나타냅니다. 지원되는 값은

::: zone pivot="azuredataexplorer"

|*시각화*     |Description|
|--------------------|-|
| `anomalychart`     | 타임차트와 유사하지만 [함수를](./series-decompose-anomaliesfunction.md) 사용하여 [series_decompose_anomalies 이상을 강조 표시됩니다.](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning) |
| `areachart`        | 지역 그래프. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `barchart`         | 첫 번째 열은 x축이며 텍스트, 날짜 시간 또는 숫자일 수 있습니다. 다른 열은 숫자이며 가로 스트립으로 표시됩니다.|
| `card`             | 첫 번째 결과 레코드는 스칼라 값 집합으로 처리되고 카드로 표시됩니다. |
| `columnchart`      | 대신 `barchart` 수평 스트립의 수직 스트립과 마찬가지로.|
| `ladderchart`      | 마지막 두 열은 x축, 다른 열은 y축입니다.|
| `linechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `pivotchart`       | 피벗 테이블과 차트를 표시합니다. 사용자는 대화식으로 데이터, 열, 행 및 다양한 차트 유형을 선택할 수 있습니다. |
| `scatterchart`     | 포인트 그래프. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `stackedareachart` | 누적 영역 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `table`            | 기본값 - 결과가 테이블로 표시됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 기타(숫자) 열은 y축입니다. 값이 숫자 열을 "그룹화"하고 차트에서 다른 줄을 만드는 데 사용되는 하나의 문자열 열이 있습니다 (추가 문자열 열은 무시됨). |
| `timepivot`        | 이벤트 타임라인에 대한 대화형 탐색(시간 축에 피벗)|

::: zone-end

::: zone pivot="azuremonitor"

|*시각화*     |Description|
|--------------------|-|
| `areachart`        | 지역 그래프. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `barchart`         | 첫 번째 열은 x축이며 텍스트, 날짜 시간 또는 숫자일 수 있습니다. 다른 열은 숫자이며 가로 스트립으로 표시됩니다.|
| `columnchart`      | 대신 `barchart` 수평 스트립의 수직 스트립과 마찬가지로.|
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `scatterchart`     | 포인트 그래프. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `table`            | 기본값 - 결과가 테이블로 표시됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 기타(숫자) 열은 y축입니다. 값이 숫자 열을 "그룹화"하고 차트에서 다른 줄을 만드는 데 사용되는 하나의 문자열 열이 있습니다 (추가 문자열 열은 무시됨).|

::: zone-end

* *PropertyName*/*PropertyValue* 렌더링 할 때 사용할 추가 정보를 나타냅니다.
  모든 속성은 선택 사항입니다. 지원되는 속성은 다음과 같습니다.

::: zone pivot="azuredataexplorer"

|*속성 이름*|*속성 값*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |각 측정값의 값이 모든 이전 측정값에 추가되는지 여부입니다. (또는)`true` `false`|
|`kind`        |시각화 종류의 추가 정교화. 아래를 참조하십시오.                         |
|`legend`      |범례를 표시할지 여부(또는)를`visible` `hidden`표시할지 여부입니다.                       |
|`series`      |레코드당 값을 결합한 열의 쉼표 구분 목록은 레코드가 속한 계열을 정의합니다.|
|`ymin`        |Y축에 표시할 최소값입니다.                                      |
|`ymax`        |Y축에 표시할 최대값입니다.                                      |
|`title`       |시각화의 제목(형식)입니다. `string`                                |
|`xaxis`       |x축(또는)의`linear` `log`배율을 조정하는 방법                                      |
|`xcolumn`     |결과의 열이 x축에 사용됩니다.                                |
|`xtitle`      |x축(유형)의 `string`제목입니다.                                       |
|`yaxis`       |y축(또는)의`linear` `log`배율을 조정하는 방법                                      |
|`ycolumns`    |x 열의 값당 제공된 값으로 구성된 쉼표 구분된 열 목록입니다.|
|`ysplit`      |여러 시각화를 분할하는 방법. 아래를 참조하십시오.                               |
|`ytitle`      |y축(유형)의 `string`제목입니다.                                       |
|`anomalycolumns`|에 대해서만 `anomalychart`관련 속성입니다. 변칙 시리즈로 간주되고 차트의 포인트로 표시되는 쉼표 구분열 목록|

::: zone-end

::: zone pivot="azuremonitor"

|*속성 이름*|*속성 값*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |시각화 종류의 추가 정교화. 아래를 참조하십시오.                         |
|`series`      |레코드당 값을 결합한 열의 쉼표 구분 목록은 레코드가 속한 계열을 정의합니다.|
|`title`       |시각화의 제목(형식)입니다. `string`                                |
|`yaxis`       |y축(또는)의`linear` `log`배율을 조정하는 방법                                      |

::: zone-end

일부 시각화는 `kind` 속성을 제공하여 더 자세히 설명할 수 있습니다.
이러한 항목은 다음과 같습니다.

|*시각화*|`kind`             |Description                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |각 "영역"은 그 자체로 서 있습니다.     |
|               |`unstacked`        |`default`와 같습니다.                 |
|               |`stacked`          |오른쪽에 "영역"을 스택합니다.        |
|               |`stacked100`       |오른쪽에 "영역"을 쌓고 각 영역을 다른 영역과 동일한 너비로 늘입니다.|
|`barchart`     |`default`          |각 "바"는 그 자체로 서 있습니다.      |
|               |`unstacked`        |`default`와 같습니다.                 |
|               |`stacked`          |스택 "막대".                      |
|               |`stacked100`       |"바드"를 쌓고 각각을 다른 폭과 동일한 너비로 늘입니다.|
|`columnchart`  |`default`          |각 "열"은 그 자체로 서 있습니다.   |
|               |`unstacked`        |`default`와 같습니다.                 |
|               |`stacked`          |"열"을 다른 기둥 위에 쌓습니다.|
|               |`stacked100`       |"열"을 쌓고 각 기둥을 다른 기둥과 동일한 높이로 늘입니다.|
|`piechart`     |`map`              |예상 열은 [경도, 위도] 또는 GeoJSON 점, 색상 축 및 숫자입니다. 쿠스토 익스플로러 데스크톱에서 지원됩니다.|
|`scatterchart` |`map`              |예상 열은 [경도, 위도] 또는 GeoJSON 지점입니다. 계열 열은 선택 사항입니다. 쿠스토 익스플로러 데스크톱에서 지원됩니다.|

::: zone pivot="azuredataexplorer"

일부 시각화는 여러 y축 값으로 분할을 지원합니다.

|`ysplit`  |Description                                                       |
|----------|------------------------------------------------------------------|
|`none`    |모든 계열 데이터에 대해 단일 y축이 표시됩니다. (기본값)       |
|`axes`    |단일 차트는 여러 y 축(계열당 하나씩)으로 표시됩니다.|
|`panels`  |각 `ycolumn` 값에 대해 하나의 차트가 렌더링됩니다(일부 제한까지).|

::: zone-end

> [!NOTE]
> 렌더 연산자의 데이터 모델은 세 가지 종류의 열이 있는 것처럼 테이블 형식 데이터를 봅니다.
>
> * x축 `xcolumn` 열(속성으로 표시)입니다.
> * 계열 `series` 열(속성에 의해 표시된 열의 수에 관계없이) 각 레코드에 대해 이러한 열의 결합된 값은 단일 계열을 정의하며 차트에는 고유한 결합 값이 있는 만큼의 계열이 있습니다.
> * y축 `ycolumns` 열(속성으로 표시된 열 수에 관계없이).
  각 레코드에 대해 계열에는 y축 열이 있는 만큼의 측정값(차트의 "점")이 있습니다.

> [!TIP]
> 
> * 을 `where` `summarize` 사용하고 `top` 표시할 볼륨을 제한합니다.
> * x축의 순서를 정의하기 위해 데이터를 정렬합니다.
> * 사용자 에이전트는 쿼리에서 지정하지 않은 속성의 값을 자유롭게 "추측"할 수 있습니다. 특히 결과의 스키마에 "흥미롭지 않은" 열이 있으면 잘못 추측할 수 있습니다. 그런 경우 이러한 열을 투영해 보십시오. 

**예제**

```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[자습서의 예제를 렌더링합니다.](./tutorial.md#render-display-a-chart-or-table)

[이상 징후 검색](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
