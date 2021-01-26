---
title: '빠른 시작: Azure Data Explorer Web UI에서 데이터 쿼리'
description: 이 빠른 시작에서는 Azure Data Explorer Web UI에서 데이터를 쿼리하고 공유하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/22/2020
ms.localizationpriority: high
ms.openlocfilehash: e2c6a54e675c85d31b44b031f78629fd1afcf8a5
ms.sourcegitcommit: 8c0674d2bc3c2e10eace5314c30adc7c9e4b3d44
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571791"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>빠른 시작: Azure Data Explorer Web UI에서 데이터 쿼리

Azure Data Explorer는 대량의 데이터를 실시간으로 분석할 수 있는 빠른 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer는 Azure Data Explorer 클러스터에 연결하고 Kusto 쿼리 언어 명령과 쿼리를 작성, 실행 및 공유할 수 있는 웹 환경을 제공합니다. 웹 환경은 Azure Portal 및 독립 실행형 웹 애플리케이션인 [Azure Data Explorer 웹 UI](https://dataexplorer.azure.com)에서 사용할 수 있습니다. Azure Data Explorer 웹 UI는 HTML iframe의 다른 웹 포털에서도 호스팅할 수 있습니다. 웹 UI 및 사용되는 모나코 편집기를 호스팅하는 방법에 대한 자세한 내용은 [모나코 IDE 통합](kusto/api/monaco/monaco-kusto.md)을 참조하세요.
이 빠른 시작에서는 독립 실행형 Azure Data Explorer 웹 UI에서 작업합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 클러스터 및 데이터베이스. [사용자 고유의 클러스터를 만들거나](create-cluster-database-portal.md) Azure Data Explorer help 클러스터를 사용합니다.

## <a name="sign-in-to-the-application"></a>애플리케이션에 로그인합니다.

[애플리케이션](https://dataexplorer.azure.com/)에 로그인합니다.

## <a name="add-clusters"></a>클러스터 추가

애플리케이션을 처음 열면 클러스터 연결이 없습니다.

![클러스터 추가](media/web-query-data/add-cluster.png)

쿼리 실행을 시작하려면 먼저 클러스터에 대한 연결을 추가해야 합니다. 이 섹션에서는 Azure Data Explorer **help** 클러스터 및 [필수 조건](#prerequisites)(선택 사항)에서 만든 테스트 클러스터에 대한 연결을 추가합니다.

### <a name="add-help-cluster"></a>help 클러스터 추가

1. 애플리케이션의 왼쪽 위에서 **클러스터 추가** 를 선택합니다.

1. **클러스터 추가** 대화 상자에서 URI(https://help.kusto.windows.net )를 입력한 다음, **추가** 를 선택합니다.
   
1. 이제 왼쪽 창에 **help** 클러스터가 표시됩니다. **Samples** 데이터베이스를 펼치고, **Tables** 폴더를 열어 액세스할 수 있는 샘플 테이블을 표시합니다.

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="help 클러스터에서 테이블 찾기":::

이 빠른 시작의 뒷부분과 다른 Azure 데이터 탐색기 문서에서는 **StormEvents** 테이블을 사용합니다. 

### <a name="add-your-cluster"></a>클러스터 추가

이제 사용자가 만든 테스트 클러스터를 추가 합니다.

1. **클러스터 추가** 를 선택합니다.

1. **클러스터 추가** 대화 상자에서 테스트 클러스터 URL을 `https://<ClusterName>.<Region>.kusto.windows.net/` 형식으로 입력하고, **추가** 를 선택합니다. 예를 들어 다음 이미지와 같이 `https://mydataexplorercluster.westus.kusto.windows.net`이 있습니다.

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="테스트 클러스터 URL 입력":::
    
1. 아래 예제에는 **help** 클러스터와 새 클러스터인 **docscluster.westus**(전체 URL: `https://docscluster.westus.kusto.windows.net/`)가 표시되어 있습니다.

    ![테스트 클러스터](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>쿼리 실행

이제 두 클러스터 모두에서 대해 쿼리를 실행할 수 있습니다(테스트 클러스터에 데이터가 있다고 가정). 이 문서의 목적을 위해 여기서는 **help** 클러스터에 집중합니다.

1. 왼쪽 창의 **help** 클러스터 아래에서 **Samples** 데이터베이스를 선택합니다.

1. 다음 쿼리를 복사하여 쿼리 창에 붙여넣습니다. 창 위쪽에서 **실행** 을 선택합니다.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    이 쿼리는 **StormEvents** 테이블에서 10개의 최신 레코드를 반환합니다. 결과는 다음 테이블과 같이 표시됩니다.

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="10개의 Storm 이벤트에 대한 데이터를 나열하는 테이블의 스크린샷" border="false":::

    다음 이미지에서는 클러스터가 추가된 애플리케이션의 상태 및 결과가 포함된 쿼리를 보여 줍니다.

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="전체 화면":::

1. 다음 쿼리를 복사하여 쿼리 창에서 첫 번째 쿼리 아래에 붙여넣습니다. 첫 번째 쿼리와 같이 별도의 줄에 형식이 지정되지 않는 방법을 확인합니다.

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. 새 쿼리를 선택합니다. *Shift+Alt+F* 를 눌러 쿼리 형식을 지정합니다. 이 쿼리는 다음 쿼리와 같습니다.

    ![서식이 지정된 쿼리](media/web-query-data/formatted-query.png)

1. **실행** 을 선택하거나 *Shift+Enter* 를 눌러 쿼리를 실행합니다. 이 쿼리는 첫 번째 쿼리와 같은 레코드를 반환하지만 `project` 문에 지정된 열만 포함합니다. 결과는 다음 테이블과 같이 표시됩니다.

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="10개의 Storm 이벤트에 대한 시작 시간, 종료 시간, 상태, 이벤트 유형, 손상 속성 및 에피소드 설명을 나열하는 테이블의 스크린샷" border="false":::

    > [!TIP]
    > 쿼리를 다시 실행하지 않고 첫 번째 쿼리의 결과 집합을 표시하려면 쿼리 창의 위쪽에서 **회수** 를 선택합니다. 분석 중에 여러 쿼리를 실행하는 경우가 많으며, **회수** 를 사용하여 이전 쿼리의 결과를 검색할 수 있습니다.

1. 쿼리를 하나 더 실행하여 다른 출력 유형을 확인해 보겠습니다.

    ```kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```

    결과는 다음 차트와 같이 표시됩니다.

    ![세로 막대형 차트](media/web-query-data/column-chart.png)

    > [!NOTE]
    > 쿼리 식의 빈 줄은 실행되는 쿼리 부분에 영향을 줄 수 있습니다.
    > * 텍스트를 선택하지 않은 경우 쿼리나 명령이 빈 줄로 구분되어 있다고 가정합니다.
    > * 텍스트를 선택하면 선택한 텍스트가 실행됩니다.

## <a name="work-with-the-table-grid"></a>테이블 모눈 사용

이제 기본 쿼리의 작동 방식을 확인했으므로 테이블 그리드를 사용하여 결과를 사용자 지정하고 추가 분석을 수행하는 방법을 살펴보겠습니다.

1. 첫 번째 쿼리를 다시 실행합니다. **State** 열 위에 마우스를 놓고 메뉴를 선택한 다음 **그룹화 방법: State** 를 선택합니다.

    ![그룹화 방법: State](media/web-query-data/group-by.png)

1. 그리드에서 **California** 를 두 번 클릭하여 해당 상태에 대한 레코드를 확장하고 확인합니다. 이러한 유형의 그룹화는 예비 분석을 수행할 때 유용할 수 있습니다.

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="California 그룹이 확장된 쿼리 결과 그리드의 스크린샷" border="false":::

1. **Group** 열 위에 마우스를 놓고 **열 다시 설정** 을 선택합니다. 이 설정은 그리드를 원래 상태로 되돌립니다.

    ![열 다시 설정](media/web-query-data/reset-columns.png)

1. 다음 쿼리를 실행합니다.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. 결과 그리드에서 몇 개의 숫자 셀을 선택합니다. 테이블 그리드를 사용하면 여러 행, 열 및 셀을 선택하고 이에 집계를 계산할 수 있습니다. 웹 UI는 현재 숫자 값에 대해 **Average**, **Count**, **Min**, **Max** 및 **Sum** 함수를 지원합니다.

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="함수 선택"::: 

1. 그리드 오른쪽에서 **열** 을 선택하여 테이블 도구 패널을 표시합니다. Excel의 피벗 테이블 필드 목록과 비슷하게 작동하는 이 패널을 통해 모눈 자체에서 추가 분석을 수행할 수 있습니다.

    ![테이블 도구 패널](media/web-query-data/tool-panel.png)

1. **피벗 모드** 를 선택한 다음, **State** 열을 **행 그룹** 으로, **DamageProperty** 열을 **값** 으로, **EventType** 열을 **열 레이블** 로 각각 끕니다.  

    ![피벗 모드](media/web-query-data/pivot-mode.png)

    결과는 다음 피벗 테이블과 같이 표시됩니다.

    ![피벗 테이블](media/web-query-data/pivot-table.png)

    Vermont와 Alabama에는 각각 같은 범주 아래에 이벤트가 두 개씩 있는 반면 Texas에는 서로 다른 범주 아래에 이벤트가 두 개 있습니다. 피벗 테이블은 이러한 차이를 빠르게 파악할 수 있도록 하므로 빠른 분석에 유용한 도구입니다.

## <a name="search-in-the-results-table"></a>결과 테이블에서 검색

결과 테이블에서 특정 식을 찾을 수 있습니다.

1.  다음 쿼리를 실행합니다.

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. 오른쪽에서 **검색** 단추를 클릭하고 *"Wabash"* 를 입력합니다.

    :::image type="content" source="media/web-query-data/search.png" alt-text="테이블에서 검색":::

1. 검색된 식에 대한 모든 멘션이 테이블에서 강조 표시됩니다. *Enter* 를 클릭하여 앞으로 이동하거나 *Shift+Enter* 를 클릭하여 뒤로 이동하여 탐색하거나, 검색 상자 옆에 있는 *위로 이동* 및 *아래로 이동* 단추를 사용할 수 있습니다.

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="검색 결과 탐색":::

## <a name="share-queries"></a>쿼리 공유

대부분의 경우에는 직접 만든 쿼리를 공유하게 됩니다. 

1. 쿼리 창에서 복사한 첫 번째 쿼리를 선택합니다.

1. 쿼리 창 위쪽에서 **공유** 를 선택합니다. 

:::image type="content" source="media/web-query-data/share-menu.png" alt-text="공유 메뉴":::

드롭다운에서 사용할 수 있는 옵션은 다음과 같습니다.
* 클립보드에 링크
* [클립보드에 쿼리 연결](#provide-a-deep-link)
* 클립보드에 연결, 쿼리, 결과 복사
* [대시보드에 고정](#pin-to-dashboard)
* [Power BI로 쿼리](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>딥 링크 제공

클러스터 액세스 권한이 있는 다른 사용자들이 쿼리를 실행할 수 있도록 딥 링크를 제공할 수 있습니다.

1. **공유** 에서 **클립보드에 연결, 쿼리** 를 선택합니다.

1. 링크와 쿼리를 텍스트 파일에 복사합니다.

1. 새 브라우저 창에 링크를 붙여넣습니다. 결과는 다음과 같습니다.

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="공유된 쿼리 딥 링크":::

### <a name="pin-to-dashboard"></a>대시보드에 고정

웹 UI에서 쿼리를 사용하여 데이터 탐색을 완료하고 필요한 데이터를 찾으면 이를 지속적으로 모니터링하기 위해 대시보드에 고정할 수 있습니다. 

쿼리를 고정하려면:

1. **공유** 에서 **대시보드에 고정** 을 선택합니다.

1. **대시보드에 고정** 창에서:
    1. **쿼리 이름** 을 입력합니다.
    1. **기존 항목 사용** 또는 **새로 만들기** 를 선택합니다.
    1. **대시보드 이름** 입력
    1. **만든 후 대시보드 보기** 확인란(새 대시보드인 경우)을 선택합니다.
    1. **고정** 을 선택합니다.

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="대시보드에 고정 창":::

> [!NOTE]
> **대시보드에 고정** 은 선택한 쿼리만 고정합니다. 대시보드 데이터 원본을 만들고 대시보드의 시각적 개체에 렌더링 명령을 변환하려면 데이터베이스 목록에서 관련 데이터베이스를 선택해야 합니다.

## <a name="export-query-results"></a>쿼리 결과 내보내기

쿼리 결과를 CSV 파일로 내보내려면 **파일** > **CSV로 내보내기** 를 선택합니다.

:::image type="content" source="media/web-query-data/export-results.png" alt-text="CSV 파일로 결과 내보내기":::

## <a name="settings"></a>설정

**설정** 탭에서 다음을 수행할 수 있습니다.

* [환경 설정 내보내기](#export-environment-settings)
* [환경 설정 가져오기](#import-environment-settings)
* [오류 수준 강조 표시](#highlight-error-levels)
* [로컬 상태 지우기](#clean-up-resources)

오른쪽 위에서 설정 아이콘(:::image type="icon" source="media/web-query-data/settings-icon.png" border="false":::)을 선택하여 **설정** 창을 엽니다.

:::image type="content" source="media/web-query-data/settings.png" alt-text="설정 창":::

### <a name="export-and-import-environment-settings"></a>환경 설정 내보내기 및 가져오기

내보내기 및 가져오기 작업은 작업 환경을 보호하고 다른 브라우저 및 디바이스에 재배치하는 데 도움이 됩니다. 내보내기 작업은 모든 설정, 클러스터 연결 및 쿼리 탭을 다른 브라우저 또는 디바이스로 가져올 수 있는 JSON 파일로 내보냅니다.

#### <a name="export-environment-settings"></a>환경 설정 내보내기

1. **설정** > **일반** 창에서 **내보내기** 를 선택합니다.
1. **adx-export.json** 파일이 로컬 스토리지에 다운로드됩니다.
1. **로컬 상태 지우기** 를 선택하여 환경을 원래 상태로 되돌립니다. 이 설정은 모든 클러스터 연결을 삭제하고 열려 있는 탭을 닫습니다.

> [!NOTE]
> **내보내기** 는 쿼리 관련 데이터만 내보냅니다. 대시보드 데이터는 **adx-export.json** 파일 내에서 내보내지 않습니다.

#### <a name="import-environment-settings"></a>환경 설정 가져오기

1. **설정** > **일반** 창에서 **가져오기** 를 선택합니다. 그런 다음, **경고** 팝업에서 **가져오기** 를 선택합니다.

    :::image type="content" source="media/web-query-data/import.png" alt-text="가져오기 경고":::

1. 로컬 스토리지에서 **adx-export.json** 파일을 찾아서 엽니다.
1. 이제 이전 클러스터 연결 및 열린 탭을 사용할 수 있습니다.

> [!NOTE]
> **가져오기** 는 모든 기존 환경 설정 및 데이터를 재정의합니다.

### <a name="highlight-error-levels"></a>오류 수준 강조 표시

Kusto는 결과 패널에서 각 행의 심각도 또는 세부 정보 표시 수준을 해석하고 이에 따라 색을 지정합니다. 이렇게 하려면 각 열의 고유 값을 알려진 패턴 세트("경고", "오류" 등)와 일치시킵니다. 

#### <a name="enable-error-level-highlighting"></a>오류 수준 강조 표시 사용

오류 수준 강조 표시를 사용하려면 다음을 수행합니다.

1. 사용자 이름 옆에 있는 **설정** 아이콘을 선택합니다.
1. **모양** 탭을 선택하고 오른쪽에 있는 **오류 수준 강조 표시 사용** 옵션을 설정/해제합니다. 

:::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="설정에서 오류 수준 강조 표시를 사용하도록 설정하는 방법을 보여 주는 애니메이션 GIF":::

**밝게** 모드의 오류 수준 색 구성표 | **어둡게** 모드의 오류 수준 색 구성표
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="밝게 모드의 색 범례 스크린샷"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="어둡게 모드의 색 범례 스크린샷":::

#### <a name="column-requirements-for-highlighting"></a>강조 표시에 대한 열 요구 사항

강조 표시된 오류 수준의 경우 열은 int, long 또는 string 유형이어야 합니다.

* 열이 `long` 또는 `int` 유형인 경우:
   * 열 이름은 *수준* 이어야 합니다.
   * 값은 1~5의 숫자만 포함할 수 있습니다.
* 열이 `string` 유형인 경우: 
   * 열 이름은 성능 향상을 위해 선택적으로 *수준* 이 될 수 있습니다. 
   * 열에는 다음 값만 포함될 수 있습니다.
       * critical, crit, fatal, assert, high
       * error, e
       * warning, w, monitor
       * 정보
       * verbose, verb, d
   
## <a name="provide-feedback"></a>피드백 제공

1. 애플리케이션의 오른쪽 위에서 피드백 아이콘(:::image type="icon" source="media/web-query-data/icon-feedback.png" border="false":::)을 선택합니다.

1. 피드백을 입력하고 **제출** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서는 리소스를 만들지 않았지만, 애플리케이션에서 클러스터 중 하나 또는 둘 다 제거하려는 경우 클러스터를 마우스 오른쪽 단추로 클릭하고 **연결 제거** 를 선택합니다.
또 다른 옵션은 **설정** > **일반** 탭에서 **로컬 상태 지우기** 를 선택하는 것입니다. 이 작업을 수행하면 모든 클러스터 연결이 제거되고 열려 있는 모든 쿼리 탭이 닫힙니다.

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기용 쿼리 작성](write-queries.md)
