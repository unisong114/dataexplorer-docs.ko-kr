---
title: render 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 렌더링 연산자에 대해 설명 합니다.
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
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783338"
---
# <a name="render-operator"></a>render 연산자

쿼리 결과를 특정 방식으로 렌더링 하도록 사용자 에이전트에 지시 합니다.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Render 연산자는 쿼리의 마지막 연산자 여야 하며 단일 테이블 형식 데이터 스트림 결과를 생성 하는 쿼리에만 사용 되어야 합니다.
> * Render 연산자는 데이터를 수정 하지 않습니다. 결과의 확장 속성에 주석 ("시각화")을 삽입 합니다. 주석에는 쿼리에 연산자가 제공 하는 정보가 포함 되어 있습니다.
> * 시각화 정보의 해석은 사용자 에이전트에 의해 수행 됩니다. 다른 에이전트 (예: Kusto. 탐색기, Kusto. WebExplorer)는 다른 시각화를 지원할 수 있습니다.

## <a name="syntax"></a>Syntax

*T* `|` `render` *시각화* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

위치:

* *시각화* 는 사용할 시각화의 종류를 나타냅니다. 지원되는 값은

::: zone pivot="azuredataexplorer"

|*시각화*     |Description|
|--------------------|-|
| `anomalychart`     | 시간 차트와 비슷하지만 [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) 함수를 사용 하 여 [변칙을 강조 표시](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning) 합니다. |
| `areachart`        | 영역 그래프입니다. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `barchart`         | 첫 번째 열은 x 축 이며 텍스트, 날짜/시간 또는 숫자일 수 있습니다. 다른 열은 숫자 이며 가로 줄무늬로 표시 됩니다.|
| `card`             | 첫 번째 결과 레코드는 스칼라 값 집합으로 처리 되 고 카드로 표시 됩니다. |
| `columnchart`      | `barchart`가로 줄무늬 대신 세로 줄무늬를 사용 하는 것과 같습니다.|
| `ladderchart`      | 마지막 두 개의 열은 x 축이 고 다른 열은 y 축입니다.|
| `linechart`        | 선 그래프입니다. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `pivotchart`       | 피벗 테이블 및 차트를 표시 합니다. 사용자는 대화형으로 데이터, 열, 행 및 다양 한 차트 종류를 선택할 수 있습니다. |
| `scatterchart`     | 요소 그래프. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `stackedareachart` | 누적 영역형 그래프입니다. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `table`            | 기본값-결과가 테이블로 표시 됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 다른 (숫자) 열은 y 축입니다. 숫자 열을 "그룹화" 하 고 차트에서 다른 선을 만드는 데 사용 되는 값을 갖는 문자열 열이 하나 있습니다 (추가 문자열 열은 무시 됨). |
| `timepivot`        | 이벤트 타임라인에 대한 대화형 탐색입니다(시간 축에서 피벗).|

::: zone-end

::: zone pivot="azuremonitor"

|*시각화*     |Description|
|--------------------|-|
| `areachart`        | 영역 그래프입니다. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `barchart`         | 첫 번째 열은 x 축 이며 텍스트, 날짜/시간 또는 숫자일 수 있습니다. 다른 열은 숫자 이며 가로 줄무늬로 표시 됩니다.|
| `columnchart`      | `barchart`가로 줄무늬 대신 세로 줄무늬를 사용 하는 것과 같습니다.|
| `piechart`         | 첫 번째 열은 색깔 축이고 두 번째 열은 숫자입니다. |
| `scatterchart`     | 요소 그래프. 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. |
| `table`            | 기본값-결과가 테이블로 표시 됩니다.|
| `timechart`        | 선 그래프입니다. 첫 번째 열은 x축이며 날짜/시간이어야 합니다. 다른 (숫자) 열은 y 축입니다. 숫자 열을 "그룹화" 하 고 차트에서 다른 선을 만드는 데 사용 되는 값을 갖는 문자열 열이 하나 있습니다 (추가 문자열 열은 무시 됨).|

::: zone-end

* *PropertyName* / *PropertyValue* 는 렌더링할 때 사용할 추가 정보를 표시 합니다.
  모든 속성은 선택 사항입니다. 지원되는 속성은 다음과 같습니다.

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |각 측정값의 값을 모든 선행 작업에 추가할지 여부를 지정 합니다. ( `true` 또는 `false` )|
|`kind`        |시각화 종류의 추가 패턴. 아래 내용을 참조하세요.                         |
|`legend`      |범례를 표시할지 (또는)를 표시할지 여부를 지정 `visible` `hidden` 합니다.                       |
|`series`      |쉼표로 구분 된 레코드의 조합 값이 레코드가 속한 계열을 정의 하는 쉼표로 구분 된 열 목록입니다.|
|`ymin`        |Y 축에 표시할 최소값입니다.                                      |
|`ymax`        |Y 축에 표시할 최대값입니다.                                      |
|`title`       |시각화의 제목입니다 (형식 `string` ).                                |
|`xaxis`       |X 축의 크기를 조정 하는 방법 ( `linear` 또는 `log` )                                      |
|`xcolumn`     |결과에서 x 축에 사용 되는 열입니다.                                |
|`xtitle`      |X 축의 제목 (형식 `string` )입니다.                                       |
|`yaxis`       |Y 축 크기를 조정 하는 방법 ( `linear` 또는 `log` )                                      |
|`ycolumns`    |X 열의 값에 따라 제공 되는 값으로 구성 된 쉼표로 구분 된 열 목록입니다.|
|`ysplit`      |여러 시각화를 분할 하는 방법입니다. 아래 내용을 참조하세요.                               |
|`ytitle`      |Y 축 (형식)의 제목입니다 `string` .                                       |
|`anomalycolumns`|에만 관련 된 속성 `anomalychart` 입니다. 변칙 계열로 간주 되 고 차트에 점으로 표시 되는 쉼표로 구분 된 열 목록입니다.|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |시각화 종류의 추가 패턴. 아래 내용을 참조하세요.                         |
|`series`      |쉼표로 구분 된 레코드의 조합 값이 레코드가 속한 계열을 정의 하는 쉼표로 구분 된 열 목록입니다.|
|`title`       |시각화의 제목입니다 (형식 `string` ).                                |
|`yaxis`       |Y 축 크기를 조정 하는 방법 ( `linear` 또는 `log` )                                      |

::: zone-end

일부 시각화는 속성을 제공 하 여 보다 구체화 될 수 있습니다 `kind` .
이러한 항목은 다음과 같습니다.

|*시각화*|`kind`             |Description                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |각 "영역"은 자체를 나타냅니다.     |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |"영역"을 오른쪽으로 쌓습니다.        |
|               |`stacked100`       |"영역"을 오른쪽으로 스택 하 고 각 항목을 다른 너비와 같은 너비로 늘립니다.|
|`barchart`     |`default`          |각 "막대"는 자체를 나타냅니다.      |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |Stack "막대".                      |
|               |`stacked100`       |"Bard"을 쌓아 각 항목을 다른 너비와 같은 너비로 늘립니다.|
|`columnchart`  |`default`          |각 "열"은 자체를 나타냅니다.   |
|               |`unstacked`        |`default`와 동일합니다.                 |
|               |`stacked`          |"Columns"를 다른 쪽으로 쌓습니다.|
|               |`stacked100`       |"Columns"를 스택과 각 항목을 다른 높이와 같은 높이로 늘립니다.|
|`scatterchart` |`map`              |필요한 열은 [경도, 위도] 또는 GeoJSON point입니다. 계열 열은 선택 사항입니다.|
|`piechart`     |`map`              |필요한 열은 [경도, 위도] 또는 GeoJSON 점, 색 축 및 숫자입니다. Kusto Explorer 데스크톱에서 지원 됩니다.|

::: zone pivot="azuredataexplorer"

일부 시각화는 여러 y 축 값으로 분할을 지원 합니다.

|`ysplit`  |Description                                                       |
|----------|------------------------------------------------------------------|
|`none`    |모든 계열 데이터에 대해 단일 y 축이 표시 됩니다. (기본값)       |
|`axes`    |단일 차트가 여러 y 축과 함께 표시 됩니다 (계열 마다 하나씩).|
|`panels`  |하나의 차트가 각 값에 대해 렌더링 됩니다 `ycolumn` (최대 제한).|

::: zone-end

> [!NOTE]
> Render 연산자의 데이터 모델은 다음과 같은 세 가지 종류의 열이 있는 것 처럼 표 형식 데이터를 찾습니다.
>
> * 속성으로 표시 되는 x 축 열 `xcolumn` 입니다.
> * 계열 열 (속성으로 표시 되는 열 수에 관계 없음 `series` ) 각 레코드에 대해 이러한 열의 결합 된 값은 단일 계열을 정의 하 고 차트는 결합 된 고유 값 만큼의 계열을 포함 합니다.
> * Y 축 열 (속성으로 표시 되는 열 수에 관계 없음 `ycolumns` )입니다.
  각 레코드에 대해 y 축 열이 있는 계열에는 측정값 (차트의 "요소")이 많이 있습니다.

> [!TIP]
> 
> * `where`, 및를 사용 `summarize` 하 여 `top` 표시 되는 볼륨을 제한 합니다.
> * 데이터를 정렬 하 여 x 축의 순서를 정의 합니다.
> * 사용자 에이전트는 쿼리에 지정 되지 않은 속성의 값을 "추측" 할 수 있습니다. 특히 결과의 스키마에 "필요 하지 않은" 열을 포함 하면 잘못 된 추측으로 변환 될 수 있습니다. 이러한 열이 발생 하는 경우 해당 열을 앞으로 이동 하십시오. 

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
