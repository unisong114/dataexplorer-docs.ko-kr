---
title: Azure 데이터 탐색기 대시보드 시각적 개체 사용자 지정
description: 간편 하 게 Azure 데이터 탐색기 대시보드 시각적 개체 사용자 지정
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 630b57dc12b3ed0fac797dcc57f3b38c13a0e476
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349522"
---
# <a name="customize-azure-data-explorer-dashboard-visuals"></a>Azure 데이터 탐색기 대시보드 시각적 개체 사용자 지정

시각적 개체는 모든 Azure 데이터 탐색기 대시보드의 필수적인 부분입니다. 이 문서에서는 다양 한 시각적 개체 형식에 대해 자세히 설명 하 고, 대시보드 사용자가 시각적 개체를 사용자 지정 하는 데 사용할 수 있는 다양 한 옵션

> [!NOTE]
> 시각적 개체 사용자 지정은 편집 모드에서 대시보드 편집기로 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure 데이터 탐색기 대시보드를 사용 하 여 데이터 시각화](azure-data-explorer-dashboards.md)

## <a name="types-of-visuals"></a>시각적 개체 유형

Azure 데이터 탐색기는 여러 가지 유형의 시각적 개체를 지원 합니다. 이 섹션에서는 이러한 시각적 개체를 그리는 데 필요한 결과 집합에 필요한 다양 한 형식의 시각적 개체 및 데이터 열에 대해 설명 합니다.

### <a name="table"></a>테이블

:::image type="content" source="media/dashboard-customize-visuals/table.png" alt-text="테이블 시각적 개체 형식":::

기본적으로 결과는 테이블로 표시 됩니다. 테이블 시각적 개체는 상세 데이터 나 복잡 한 데이터를 제공 하는 데 가장 적합 합니다.

### <a name="bar-chart"></a>가로 막대형 차트

:::image type="content" source="media/dashboard-customize-visuals/bar-chart.png" alt-text="가로 막대형 차트 시각적 개체 형식":::

가로 막대형 차트 시각적 개체에는 쿼리 결과에 최소 두 개의 열이 필요 합니다. 기본적으로 첫 번째 열은 y 축으로 사용 됩니다. 이 열은 text, datetime 또는 numeric 데이터 형식을 포함할 수 있습니다. 다른 열은 x 축으로 사용 되며 가로 선으로 표시 되는 숫자 데이터 형식을 포함 합니다. 가로 막대형 차트는 숫자 및 명목상 불연속 값을 비교 하는 데 주로 사용 됩니다. 여기서 각 줄의 길이는 해당 값을 나타냅니다.

### <a name="column-chart"></a>세로 막대형 차트

:::image type="content" source="media/dashboard-customize-visuals/column-chart.png" alt-text="세로 막대형 차트 시각적 개체 형식":::

세로 막대형 차트 시각적 개체에는 최소한 두 개의 열이 쿼리 결과에 필요 합니다. 기본적으로 첫 번째 열은 x 축으로 사용 됩니다. 이 열은 text, datetime 또는 numeric 데이터 형식을 포함할 수 있습니다. 다른 열은 y 축으로 사용 되며 세로 선으로 표시 되는 숫자 데이터 형식을 포함 합니다. 세로 막대형 차트는 주 범주 범위의 특정 하위 범주 항목을 비교 하는 데 사용 됩니다. 여기서 각 줄의 길이는 해당 값을 나타냅니다.

### <a name="area-chart"></a>영역형 차트

:::image type="content" source="media/dashboard-customize-visuals/area-chart.png" alt-text="영역 차트 시각적 개체 형식":::

영역형 차트 시각적 개체는 시계열 관계를 보여 줍니다. 쿼리의 첫 번째 열은 숫자 여야 하며 x 축으로 사용 됩니다. 다른 숫자 열은 y 축입니다. 꺾은선형 차트와 달리 영역형 차트도 볼륨을 시각적으로 나타냅니다. 영역형 차트는 서로 다른 데이터 집합 간의 변경을 나타내는 데 적합 합니다.

### <a name="line-chart"></a>꺾은선형 차트

:::image type="content" source="media/dashboard-customize-visuals/line-chart.png" alt-text="꺾은선형 차트 시각적 개체 형식":::

꺾은선형 차트 시각적 개체는 가장 기본적인 차트 유형입니다. 쿼리의 첫 번째 열은 숫자 여야 하며 x 축으로 사용 됩니다. 다른 숫자 열은 y 축입니다. 꺾은선형 차트는 짧고 긴 기간 동안 변경 내용을 추적 합니다. 작은 변화가 있을 경우 막대 그래프 보다 선 그래프가 더 유용 합니다.

### <a name="stat"></a>우편함

:::image type="content" source="media/dashboard-customize-visuals/stat.png" alt-text="stat 시각적 개체 형식":::

Stat 시각적 개체에는 하나의 요소만 표시 됩니다. 출력에 열과 행이 여러 개 있는 경우 stat는 첫 번째 열의 첫 번째 요소를 표시 합니다. Stat 카드는 대시보드에서 Kpi를 강조 표시 하는 데 유용 합니다.

### <a name="multi-stat"></a>다중 stat

:::image type="content" source="media/dashboard-customize-visuals/multistat.png" alt-text="다중 stat 시각적 개체 형식":::

다중 stat 시각적 개체는 단일 쿼리 결과를 사용 하 여 그룹에 여러 개의 stat 카드를 표시 합니다. 이를 위해서는 두 개의 열, 즉 레이블에 대 한 열과 값에 대 한 열이 필요 합니다. 사용자는 시각적 서식 옵션을 사용 하 여 행 및 열 수를 선택 하 여 표시할 출력 레이아웃을 사용자 지정할 수 있습니다. 이 서식 옵션은 더 편리할 뿐만 아니라 고객이 유사한 쿼리를 사용 하 여 여러 통계를 작성 하는 클러스터 부하를 줄입니다.

### <a name="pie-chart"></a>원형 차트

:::image type="content" source="media/dashboard-customize-visuals/pie-chart.png" alt-text="원형 차트 시각적 개체 형식":::

원형 차트 시각적 개체에는 쿼리 결과에 최소 두 개의 열이 필요 합니다. 기본적으로 첫 번째 열은 색 축으로 사용 됩니다. 이 열은 text, datetime 또는 numeric 데이터 형식을 포함할 수 있습니다. 다른 열은 각 조각의 크기를 결정 하는 데 사용 되 고 숫자 데이터 형식을 포함 합니다. 원형 차트는 범주 및 전체에 대 한 비율을 결합 하는 데 사용 됩니다.

### <a name="scatter-chart"></a>분산형 차트

:::image type="content" source="media/dashboard-customize-visuals/scatter-chart.png" alt-text="분산형 차트 시각적 개체 형식":::

분산형 차트 시각적 개체에서 첫 번째 열은 x 축 이며 숫자 열 이어야 합니다. 다른 숫자 열은 y 축입니다. 분산형 플롯은 변수 간의 관계를 관찰 하는 데 사용 됩니다.

### <a name="time-chart"></a>시간 차트 

:::image type="content" source="media/dashboard-customize-visuals/time-chart.png" alt-text="시간 차트 시각적 개체 형식":::

시간 차트 시각적 개체는 선 그래프의 유형입니다. 쿼리의 첫 번째 열은 x 축 이며 datetime 이어야 합니다. 다른 숫자 열은 y 축입니다. 단일 문자열 열 값은 숫자 열을 그룹화 하 고 차트에서 다른 선을 만드는 데 사용 됩니다. 다른 문자열 열은 무시 됩니다. 시간 차트 시각적 개체는 x 축이 항상 시간을 제외 하 고 [꺾은선형 차트](#line-chart) 와 비슷합니다.

### <a name="anomaly-chart"></a>변칙 차트 

:::image type="content" source="media/dashboard-customize-visuals/anomaly-chart.png" alt-text="변칙 차트 시각적 개체 형식":::

이상 차트 시각적 개체는 [시간 차트](#time-chart)와 비슷하지만 함수를 사용 하 여 변칙을 강조 표시 `series_decompose_anomalies` 합니다.

### <a name="map"></a>지도

:::image type="content" source="media/dashboard-customize-visuals/map.png" alt-text="지도 표시 유형":::

지도 시각적 개체를 렌더링 하려면 쿼리에 4 개의 열이 필요 합니다.
* 가리키기 레이블에 사용 되는 문자열 (첫 번째 열)
* 경도 (실제)
* 위도 (실제)
* 거품 크기 (int)입니다. 다른 크기가 필요 하지 않은 경우이 열은 일정 한 열이 될 수 있습니다.

맵은 지역 좌표를 사용 하 여 데이터를 시각화 하는 데 유용 합니다. 지도 시각적 개체에는 기본 제공 확대/축소 기능도 있습니다.

## <a name="customize-visuals"></a>시각적 개체 사용자 지정

1. 대시보드 메뉴에서 **편집** 을 선택 하 여 편집 모드로 전환 합니다.
1. 카드의 시각적 사용자 지정 대화 상자에 액세스 하려면 드롭다운 메뉴 > **카드 편집** 을 클릭 합니다. 또는 **쿼리 추가** 를 사용 하 여 새 카드를 만들 때 **카드 편집** 을 선택 합니다.

:::image type="content" source="media/dashboard-customize-visuals/edit-card.png" alt-text="시각적 사용자 지정을 위한 카드 편집":::

### <a name="select-properties-to-customize-visuals"></a>시각적 개체를 사용자 지정 하려면 속성을 선택 합니다.

시각적 개체를 사용자 지정 하려면 다음 속성을 사용 합니다.

:::image type="content" source="media/dashboard-customize-visuals/visual-customization-sidebar.png" alt-text="시각적 사용자 지정 세로 막대":::

|섹션  |Description | 시각적 개체 유형
|---------|---------|-----|
|**일반**    |    **누적** 또는 **누적 되지 않은** 차트 형식 선택  | 가로 막대형, 세로 막대형 및 영역형 차트 |
|**Data**    |   시각적 개체에 대 한 **Y 및 X 열** 을 선택 합니다. 플랫폼에서 쿼리 결과를 기반으로 열을 자동으로 선택 하 게 하려면 선택 항목을 **유추** 로 유지 합니다.    |가로 막대형, 세로 막대형, 분산형 및 변칙 차트|
|**범례**    |   시각적 개체에 대 한 범례의 표시를 표시 하거나 숨기려면 토글   |가로 막대형, 세로 막대형, 영역형, 꺾은선형, 분산형, 비정상 및 시간 차트 |
|**Y 축**     |   Y 축 속성을 사용자 지정할 수 있습니다. <ul><li>**레이블** : 사용자 지정 레이블의 텍스트입니다. </li><li>**Maximum value** : Y 축의 최 댓 값을 변경 합니다.  </li><li>**최 솟 값** : Y 축의 최 솟 값을 변경 합니다.  </li></ul>      |가로 막대형, 세로 막대형, 영역형, 꺾은선형, 분산형, 비정상 및 시간 차트 |
|**X축**     |    X 축 속성을 사용자 지정할 수 있습니다. <li>**레이블** : 사용자 지정 레이블의 텍스트입니다. </li>     | 가로 막대형, 세로 막대형, 영역형, 꺾은선형, 분산형, 비정상 및 시간 차트|
|**링크 렌더링**     |    테이블에서 "https://"로 시작 하는 링크를 클릭할 수 있도록 설정 합니다. <li>**열에 적용** : URL을 포함 하는 열을 선택 합니다. </li>     | 테이블|
|**레이아웃**     |    다중 stat 시각적 개체에 대 한 레이아웃 구성을 선택 합니다. <li>**열에 적용** : URL을 포함 하는 열을 선택 합니다. </li>     | 다중 stat|

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 대시보드에서 매개 변수 사용](dashboard-parameters.md)
* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md) 
