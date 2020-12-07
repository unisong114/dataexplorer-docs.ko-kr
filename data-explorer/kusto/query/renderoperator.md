---
title: render 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 render 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5670f3f9c7aa8b3d6b10f88433d19246e2daf6d6
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783338"
---
# <a name="render-operator"></a>render 연산자

쿼리 결과를 특정 방식으로 렌더링하도록 사용자 에이전트에 지시합니다.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * render 연산자는 쿼리의 마지막 연산자여야 하며, 단일 테이블 형식의 데이터 스트림 결과를 생성하는 쿼리에만 사용해야 합니다.
> * render 연산자는 데이터를 수정하지 않습니다. 주석("시각화")을 결과의 확장 속성에 삽입합니다. 주석에는 연산자에서 쿼리에 제공하는 정보가 포함됩니다.
> * 시각화 정보의 해석은 사용자 에이전트를 통해 수행됩니다. 다른 에이전트(예 : Kusto.Explorer,Kusto.WebExplorer)에서 다른 시각화를 지원할 수 있습니다.

## <a name="syntax"></a>Syntax

*T* `|` `render` *Visualization* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

여기서

* *Visualization* 은 사용할 시각화의 종류를 나타냅니다. 지원되는 값은

::: zone pivot="azuredataexplorer"

|*시각화*     |설명|
|--------------------|-|
| `anomalychart`     | timechart와 비슷하지만 [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) 함수를 사용하여 [변칙을 강조 표시](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)합니다. |
| `areachart`        | 영역형 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `barchart`         | 첫 번째 열은 x 축이며 텍스트, 날짜/시간 또는 숫자일 수 있습니다. 다른 열은 숫자이며 가로 스트립으로 표시됩니다.|
| `card`             | 첫 번째 결과 레코드는 스칼라 값 집합으로 처리되고 카드로 표시됩니다. |
| `columnchart`      | 가로 스트립 대신 세로 스트립이 있는 `barchart`와 같습니다.|
| `ladderchart`      | 마지막 두 열은 x축이고, 다른 열은 y축입니다.|
| `linechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `pivotchart`       | 피벗 테이블 및 차트를 표시합니다. 사용자는 데이터, 열, 행 및 다양한 차트 종류를 대화형으로 선택할 수 있습니다. |
| `scatterchart`     | 점 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `stackedareachart` | 누적 영역형 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `table`            | 기본값 - 결과가 테이블로 표시됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 다른(숫자) 열은 y축입니다. 숫자 열을 "그룹화"하고 차트에서 다른 선을 만드는 데 사용되는 값이 있는 하나의 문자열 열이 있습니다(추가 문자열 열은 무시됨). |
| `timepivot`        | 이벤트 타임라인에 대한 대화형 탐색입니다(시간 축에서 피벗).|

::: zone-end

::: zone pivot="azuremonitor"

|*시각화*     |설명|
|--------------------|-|
| `areachart`        | 영역형 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `barchart`         | 첫 번째 열은 x 축이며 텍스트, 날짜/시간 또는 숫자일 수 있습니다. 다른 열은 숫자이며 가로 스트립으로 표시됩니다.|
| `columnchart`      | 가로 스트립 대신 세로 스트립이 있는 `barchart`와 같습니다.|
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `scatterchart`     | 점 그래프입니다. 첫 번째 열은 x축이며 숫자 열이어야 합니다. 다른 숫자 열은 y축입니다. |
| `table`            | 기본값 - 결과가 테이블로 표시됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 다른(숫자) 열은 y축입니다. 숫자 열을 "그룹화"하고 차트에서 다른 선을 만드는 데 사용되는 값이 있는 하나의 문자열 열이 있습니다(추가 문자열 열은 무시됨).|

::: zone-end

* *PropertyName*/*PropertyValue* 는 렌더링할 때 사용할 추가 정보를 나타냅니다.
  모든 속성은 선택 사항입니다. 지원되는 속성은 다음과 같습니다.

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |각 측정값이 모든 선행 작업에 추가되는지 여부입니다. (`true` 또는 `false`)|
|`kind`        |시각화 종류에 대한 추가 상세 정보입니다. 아래 내용을 참조하세요.                         |
|`legend`      |범례를 표시하는지 여부입니다(`visible` 또는 `hidden`).                       |
|`series`      |레코드가 속한 계열을 정의하는 결합된 레코드당 값이 있는 열의 쉼표로 구분된 목록입니다.|
|`ymin`        |Y축에 표시할 최솟값입니다.                                      |
|`ymax`        |Y축에 표시할 최댓값입니다.                                      |
|`title`       |시각화의 제목입니다(`string` 형식).                                |
|`xaxis`       |x축의 크기를 조정하는 방법입니다(`linear` 또는 `log`).                                      |
|`xcolumn`     |결과에서 x축에 사용되는 열입니다.                                |
|`xtitle`      |x축의 제목입니다(`string` 형식).                                       |
|`yaxis`       |y축의 크기를 조정하는 방법입니다(`linear` 또는 `log`).                                      |
|`ycolumns`    |x 열의 값을 기준으로 제공된 값으로 구성되는 열의 쉼표로 구분된 목록입니다.|
|`ysplit`      |여러 시각화를 분할하는 방법입니다. 아래 내용을 참조하세요.                               |
|`ytitle`      |y축의 제목입니다(`string` 형식).                                       |
|`anomalycolumns`|`anomalychart`에만 관련된 속성입니다. 변칙 계열로 간주되고 차트에서 점으로 표시되는 열의 쉼표로 구분된 목록입니다.|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |시각화 종류에 대한 추가 상세 정보입니다. 아래 내용을 참조하세요.                         |
|`series`      |레코드가 속한 계열을 정의하는 결합된 레코드당 값이 있는 열의 쉼표로 구분된 목록입니다.|
|`title`       |시각화의 제목입니다(`string` 형식).                                |
|`yaxis`       |y축의 크기를 조정하는 방법입니다(`linear` 또는 `log`).                                      |

::: zone-end

일부 시각화는 `kind` 속성을 제공하여 자세히 구체화할 수 있습니다.
이러한 항목은 다음과 같습니다.

|*시각화*|`kind`             |설명                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |각 "영역" 자체를 나타냅니다.     |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |"영역"을 오른쪽으로 쌓습니다.        |
|               |`stacked100`       |"영역"을 오른쪽으로 쌓고, 각 영역을 다른 영역과 같은 너비로 늘립니다.|
|`barchart`     |`default`          |각 "가로 막대" 자체를 나타냅니다.      |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |"가로 막대"를 쌓습니다.                      |
|               |`stacked100`       |"가로 막대"를 쌓고, 각 가로 막대를 다른 가로 막대와 같은 너비로 늘립니다.|
|`columnchart`  |`default`          |각 "세로 막대" 자체를 나타냅니다.   |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |"세로 막대"를 켜켜이 쌓습니다.|
|               |`stacked100`       |"세로 막대"를 쌓고, 각 세로 막대를 다른 세로 막대와 같은 높이로 늘립니다.|
|`scatterchart` |`map`              |필요한 열은 [경도, 위도] 또는 GeoJSON 점입니다. 계열 열은 선택 사항입니다.|
|`piechart`     |`map`              |필요한 열은 [경도, 위도] 또는 GeoJSON 점, 색 축 및 숫자입니다. Kusto 탐색기 데스크톱에서 지원됩니다.|

::: zone pivot="azuredataexplorer"

일부 시각화는 여러 y축 값으로 분할하도록 지원합니다.

|`ysplit`  |설명                                                       |
|----------|------------------------------------------------------------------|
|`none`    |모든 계열 데이터에 대해 단일 y축이 표시됩니다. (기본값)       |
|`axes`    |단일 차트가 여러 y축으로 표시됩니다(계열당 하나씩).|
|`panels`  |각 `ycolumn` 값에 대해 하나의 차트가 렌더링됩니다(특정 제한까지).|

::: zone-end

> [!NOTE]
> render 연산자의 데이터 모델은 다음 세 가지 종류의 열이 있는 것처럼 테이블 형식 데이터를 검토합니다.
>
> * x축 열(`xcolumn` 속성으로 표시됨)
> * 계열 열(`series` 속성으로 표시되는 열 수). 각 레코드에 대해 이러한 열의 결합된 값은 단일 계열을 정의하며, 차트에는 결합된 고유 값만큼 많은 계열이 포함됩니다.
> * y축 열(`ycolumns` 속성으로 표시되는 열 수).
  각 레코드에 대해 계열에는 y축 열 수만큼 많은 측정값(차트의 "점")이 포함됩니다.

> [!TIP]
> 
> * `where`, `summarize` 및 `top`을 사용하여 표시되는 볼륨을 제한합니다.
> * 데이터를 정렬하여 x축의 순서를 정의합니다.
> * 사용자 에이전트는 쿼리에서 지정되지 않은 속성의 값을 자유롭게 "추측"합니다. 특히 결과의 스키마에 "관심 없는" 열이 포함되면 잘못된 추측으로 변환될 수 있습니다. 이러한 열이 발생하는 경우 해당 열을 프로젝션해 보세요. 

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[자습서의 렌더링 예제](./tutorial.md#displaychartortable)

[이상 감지](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)

::: zone-end
