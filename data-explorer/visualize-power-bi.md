---
title: '자습서: Power BI에서 Azure Data Explorer의 데이터 시각화'
description: 이 자습서에서는 Power BI를 사용하여 Azure Data Explorer에 연결하여 데이터를 시각화하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: d37cef094bfc1200182f7cf23dc68d396c126eb2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81491958"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>자습서: Power BI에서 Azure Data Explorer의 데이터 시각화

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Power BI는 데이터를 시각화하고 조직 전체에서 결과를 공유할 수 있는 비즈니스 분석 솔루션입니다. 이 자습서에서는 먼저 Azure Data Explorer에서 시각적 개체를 렌더링하는 방법을 알아봅니다. 그 다음, 연결 Power BI를 사용하여 Azure Data Explorer에 연결하고, 샘플 데이터를 기반으로 보고서를 작성하고, Power BI 서비스에 보고서를 게시합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Power BI Pro에 등록하지 않은 경우 시작하기 전에 [평가판에 등록](https://app.powerbi.com/signupredirect?pbi_source=web)합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Data Explorer에서 시각적 개체 렌더링
> * Power BI Desktop에서 Azure Data Explorer에 연결
> * Power BI Desktop에서 데이터 작업
> * 시각적 개체 관련 보고서 만들기
> * 보고서 게시 및 공유

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 및 Power BI 구독 외에 다음 항목이 필요합니다.

* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

* [StormEvents 샘플 데이터](ingest-sample-data.md) [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/)(**무료 다운로드** 선택)

## <a name="render-visuals-in-azure-data-explorer"></a>Azure Data Explorer에서 시각적 개체 렌더링

Power BI에 들어가기 전에 Azure Data Explorer에서 시각적 개체를 렌더링하는 방법을 살펴보겠습니다. 몇몇 빠른 분석에 적합한 방법입니다.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com)에 로그인합니다.

1. 왼쪽 창에서 StormEvents 샘플 데이터가 포함된 테스트 데이터베이스를 선택합니다.

1. 오른쪽 창에 다음 쿼리를 붙여넣고 **실행**을 선택합니다.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    이 쿼리는 주별으로 날씨 이벤트를 계산합니다. 그런 다음, 1800개를 넘는 날씨 이벤트가 있는 모든 주에 대해 세로 막대형 차트를 렌더링합니다.

    ![이벤트 세로 막대형 차트](media/visualize-power-bi/events-column-chart.png)

1. 오른쪽 창에 다음 쿼리를 붙여넣고 **실행**을 선택합니다.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    이 쿼리는 워싱턴 주의 7월에 대해 유형별로 날씨 이벤트를 계산합니다. 그 다음, 각 이벤트 유형의 백분율을 보여 주는 원형 차트를 렌더링합니다.

    ![이벤트 원형 차트](media/visualize-power-bi/events-pie-chart.png)

이제 Power BI를 살펴볼 차례이지만 Azure Data Explorer에서 시각적 개체를 사용하여 수행할 수 있는 더 많은 작업이 있습니다.

## <a name="connect-to-azure-data-explorer"></a>Azure Data Explorer에 연결

이제 Power BI Desktop에서 Azure Data Explorer에 연결합니다.

1. Power BI Desktop의 **홈** 탭에서 **데이터 가져오기**, **자세히**를 차례로 클릭합니다.

    ![데이터 가져오기](media/visualize-power-bi/get-data-more.png)

1. *Azure Data Explorer*를 검색한 다음, **Azure Data Explorer(베타)** , **연결**을 차례로 선택합니다.

    ![데이터 검색 및 가져오기](media/visualize-power-bi/search-get-data.png)

1. **커넥터 미리 보기** 화면에서 **계속**을 선택합니다.

1. 다음 화면에서 테스트 클러스터 및 데이터베이스의 이름을 입력합니다. 클러스터는 `https://<ClusterName>.<Region>.kusto.windows.net` 형식이어야 합니다. 테이블 이름으로 *StormEvents*를 입력합니다. 다른 모든 옵션은 기본값으로 두고 **확인**을 선택합니다.

    ![클러스터, 데이터베이스, 테이블 옵션](media/visualize-power-bi/cluster-database-table.png)

1. 데이터 미리 보기 화면에서 **편집**을 선택합니다.

    테이블이 파워 쿼리 편집기에서 열리고, 여기서 데이터를 가져오기 전에 행과 열을 편집할 수 있습니다.

## <a name="work-with-data-in-power-bi-desktop"></a>Power BI Desktop에서 데이터 작업

Azure Data Explorer에 연결했으므로 파워 쿼리 편집기에서 데이터를 편집할 수 있습니다. **BeginLat** 열에서 null 값을 가진 행을 삭제하고 **StormSummary** JSON 열 전체를 삭제합니다. 이러한 작업은 간단한 작업이지만, 데이터를 가져올 때 복잡한 변환을 수행할 수도 있습니다.

1. **BeginLat** 열에 대한 화살표를 선택하고 **null** 확인란을 선택 취소한 다음, **확인**을 선택합니다.

    ![필터 열](media/visualize-power-bi/filter-column.png)

1. **StormSummary** 열 헤더를 마우스 오른쪽 단추로 클릭한 다음, **제거**를 선택합니다.

    ![열 제거](media/visualize-power-bi/remove-column.png)

1. **쿼리 설정** 창에서 이름을 *Query1*에서 *StormEvents*로 변경합니다.

    ![쿼리 이름 변경](media/visualize-power-bi/query-name.png)

1. 리본의 **홈** 탭에서 **닫기 및 적용**을 선택합니다.

    ![닫기 및 적용](media/visualize-power-bi/close-apply.png)

    파워 쿼리에서 변경 내용을 적용한 다음, 샘플 데이터를 *데이터 모델*로 가져옵니다. 다음 몇 단계에서는 해당 모델을 보강하는 방법을 보여 줍니다. 다시 말해, 이 예제는 무엇이 가능한지 보여 주는 간단한 예제입니다.

1. 주 창의 왼쪽에서 데이터 뷰를 선택합니다.

    ![데이터 뷰](media/visualize-power-bi/data-view.png)

1. 리본의 **모델링** 탭에서 **새 열**을 선택합니다.

    ![새 열](media/visualize-power-bi/new-column.png)

1. 수식 입력줄에 다음 DAX(데이터 분석 식) 수식을 입력한 다음, Enter 키를 누릅니다.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![수식 입력줄](media/visualize-power-bi/formula-bar.png)

    이 수식은 각 날씨 이벤트의 지속 시간을 계산하는 *DurationHours* 열을 만듭니다. 다음 섹션의 시각적 개체에서 이 열이 사용됩니다.

1. 열을 보려면 테이블의 오른쪽으로 스크롤하세요.

## <a name="create-a-report-with-visuals"></a>시각적 개체 관련 보고서 만들기

데이터를 가져오고 데이터 모델을 개선했으므로 시각적 개체를 사용하여 보고서를 작성할 때입니다. 이벤트 기간 및 작물 피해를 보여 주는 지도에 따라 세로 막대형 차트를 추가합니다.

1. 창의 왼쪽에서 보고서 뷰를 선택합니다.

    ![보고서 뷰](media/visualize-power-bi/report-view.png)

1. **시각화** 창에서 묶은 세로 막대형 차트를 선택합니다.

    ![세로 막대형 차트 추가](media/visualize-power-bi/add-column-chart.png)

    빈 차트가 캔버스에 추가됩니다.

    ![빈 차트](media/visualize-power-bi/blank-chart.png)

1. **필드** 목록에서 **DurationHours** 및 **주**를 선택합니다.

    ![필드 선택](media/visualize-power-bi/select-fields.png)

    이제 1년 동안의 주별 날씨 이벤트에 대한 총 시간을 보여 주는 차트가 생성되었습니다.

    ![기간 세로 막대형 차트](media/visualize-power-bi/duration-column-chart.png)

1. 캔버스에서 세로 막대형 차트 외부의 아무 곳이나 클릭합니다.

1. **시각화** 창에서 지도를 선택합니다.

    ![지도 추가](media/visualize-power-bi/add-map.png)

1. **필드** 목록에서 **CropDamage** 및 **주**를 선택합니다. 미국 주를 명확하게 볼 수 있도록 지도 크기를 조정합니다.

    ![작물 피해 지도](media/visualize-power-bi/crop-damage-map.png)

    거품의 크기는 작물 피해 규모를 달러 값으로 나타냅니다. 세부 정보를 보려면 거품 위로 마우스를 이동합니다.

1. 보고서가 다음 이미지처럼 표시되도록 시각적 개체를 이동하고 크기를 조정합니다.

    ![완성된 보고서](media/visualize-power-bi/finished-report.png)

1. 보고서의 이름을 *storm-events.pbix*로 저장합니다.

## <a name="publish-and-share-the-report"></a>보고서 게시 및 공유

이 시점까지 Power BI에서 수행한 모든 작업은 Power BI Desktop을 사용하여 로컬로 수행되었습니다. 이제 다른 사용자와 공유할 수 있는 Power BI 서비스에 보고서를 게시합니다.

1. Power BI Desktop의 리본에 있는 **홈** 탭에서 **게시**를 선택합니다.

    ![게시 단추](media/visualize-power-bi/publish-button.png)

1. 아직 Power BI에 로그인하지 않은 경우 로그인 프로세스를 수행합니다.

1. **내 작업 영역**을 선택한 다음, **선택**을 선택합니다.

    ![작업 영역 선택](media/visualize-power-bi/select-workspace.png)

1. 게시가 완료되면 **Power BI에서 storm-events.pbix 열기**를 선택합니다.

    ![게시 성공](media/visualize-power-bi/publishing-succeeded.png)

    Power BI Desktop에서 정의된 동일한 시각적 개체 및 레이아웃을 사용하여 서비스에서 보고서가 열립니다.

1. 보고서의 오른쪽 위 모서리에서 **공유**를 선택합니다.

    ![공유 단추](media/visualize-power-bi/share-button.png)

1. **보고서 공유** 화면에서 조직에 동료를 추가하고 메모를 추가한 다음, **공유**를 선택합니다.

    ![보고서 공유](media/visualize-power-bi/share-report.png)

    동료에게 적절한 권한이 있는 경우 공유된 보고서에 액세스할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 보고서를 유지하지 않으려는 경우 *storm-events.pbix* 파일을 삭제하면 됩니다. 게시된 보고서를 제거하려면 다음 단계를 수행합니다.

1. **내 작업 영역** 아래에서 **보고서**로 스크롤 하여 **storm-events**를 찾습니다.

1. **storm-events** 옆의 줄임표( **...** )를 선택한 다음, **제거**를 선택합니다.

    ![보고서 제거](media/visualize-power-bi/remove-report.png)

1. 제거를 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [쿼리 작성](write-queries.md)
