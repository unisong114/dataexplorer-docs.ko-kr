---
title: Kusto.Explorer 설치 및 사용자 인터페이스
description: Kusto.Explorer의 기능 및 이 애플리케이션이 데이터를 검색하는 데 도움이 되는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 0086fb9f649d7bb3b7031521812c1dff0ca532f7
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513066"
---
# <a name="kustoexplorer-installation-and-user-interface"></a>Kusto.Explorer 설치 및 사용자 인터페이스

Kusto.Explorer는 사용하기 쉬운 사용자 인터페이스에서 Kusto 쿼리 언어를 사용하여 데이터를 검색할 수 있도록 하는 풍부한 데스크톱 애플리케이션입니다. 이 개요에서는 Kusto.Explorer 설정을 시작하는 방법과 사용할 사용자 인터페이스에 대해 설명합니다.

Kusto.Explorer를 사용하면 다음을 수행할 수 있습니다.
* [데이터를 쿼리](kusto-explorer-using.md#query-mode)합니다.
* 테이블에서 [데이터를 검색](kusto-explorer-using.md#search-mode)합니다.
* [데이터를 다양한 그래프로 시각화](#visualizations-section)합니다.
* 이메일 또는 딥 링크를 사용하여 [쿼리 및 결과를 공유](kusto-explorer-using.md#share-queries-and-results)합니다.

## <a name="installing-kustoexplorer"></a>Kusto.Explorer 설치

* 다음에서 Kusto.Explorer 도구를 다운로드 및 설치합니다.
     * [https://aka.ms/ke](https://aka.ms/ke)(CDN 위치)
     * [https://aka.ms/ke-mirror](https://aka.ms/ke-mirror)(비 CDN 위치)

* 대신 `https://<your_cluster>.<region>.kusto.windows.net.`에서 브라우저를 사용하여 Kusto 클러스터에 액세스합니다.
   &lt;your_cluster&gt;와 &lt;지역&gt;을 Azure Data Explorer 클러스터 이름과 배포 지역으로 바꿉니다.

### <a name="using-chrome-and-kustoexplorer"></a>Chrome 및 Kusto.Explorer 사용

Chrome을 기본 브라우저로 사용하는 경우 Chrome용 ClickOnce 확장 버전을 설치해야 합니다.

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>사용자 인터페이스 개요

Kusto.Explorer 사용자 인터페이스는 다른 Microsoft 제품과 비슷한 탭 및 패널을 기반으로 하는 레이아웃을 사용하여 설계되었습니다. 

1. [메뉴 패널](#menu-panel)의 탭을 탐색하여 다양한 작업을 수행합니다.
2. [연결 패널](#connections-panel)에서 연결을 관리합니다.
3. 스크립트 패널에서 실행할 스크립트를 만듭니다.
4. 결과 패널에서 스크립트 결과를 확인합니다.

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto Explorer 시작":::

## <a name="menu-panel"></a>메뉴 패널

Kusto.Explorer 메뉴 패널에 있는 탭은 다음과 같습니다.

* [Home](#home-tab)
* [파일](#file-tab)
* [연결](#connections-tab)
* [보기](#view-tab)
* [도구](#tools-tab)
* [Monitoring](#monitoring-tab)
* [관리](#management-tab)
* [도움말](#help-tab)

### <a name="home-tab"></a>홈 탭

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto Explorer 홈 탭":::

[홈] 탭에는 가장 최근에 사용한 함수가 섹션으로 구분되어 표시됩니다.

* [쿼리](#query-section)
* [공유](#share-section)
* [시각화](#visualizations-section)
* [보기](#view-section)
* [도움말](#help-tab) 

### <a name="query-section"></a>쿼리 섹션

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="Kusto Explorer 쿼리 메뉴":::

|메뉴|    동작|
|----|----------|
|모드 드롭다운 | <ul><li>쿼리 모드: 쿼리 창을 [스크립트 모드](kusto-explorer-using.md#query-mode)로 전환합니다. 명령을 로드하여 스크립트로 저장할 수 있습니다(기본값).</li> <li> 검색 모드: 입력한 각 명령을 즉시 처리하고 결과를 결과 창에 표시하는 단일 쿼리 모드입니다.</li> <li>검색++ 모드: 하나 이상의 테이블에서 검색 구문을 사용하여 용어를 검색할 수 있습니다. [검색++ 모드](kusto-explorer-using.md#search-mode)를 사용하는 방법에 대해 자세히 알아보세요.</li></ul> |
|새 탭| Kusto를 쿼리할 수 있는 새 탭을 엽니다. |

### <a name="share-section"></a>공유 섹션

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto Explorer 공유 메뉴":::

|메뉴|    동작|
|----|----------|
|데이터를 클립보드로|    쿼리 및 데이터 세트를 클립보드로 내보냅니다. 차트가 표시되면 차트를 비트맵으로 내보냅니다.| 
|결과를 클립보드로| 데이터 세트를 클립보드로 내보냅니다. 차트가 표시되면 차트를 비트맵으로 내보냅니다.| 
|쿼리를 클립보드로| 쿼리를 클립보드로 내보냅니다.|

### <a name="visualizations-section"></a>시각화 섹션

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto Explorer 시각화 메뉴":::

|메뉴         | 동작|
|-------------|---------|
|영역형 차트      | X 축이 첫 번째 열(숫자여야 함)인 영역형 차트를 표시합니다. 모든 숫자 열은 다른 계열(Y 축)에 매핑됩니다. |
|세로 막대형 차트 | 모든 숫자 열이 서로 다른 계열(Y 축)에 매핑되는 세로 막대형 차트를 표시합니다. 숫자 앞의 텍스트 열은 X 축입니다(UI에서 제어할 수 있음).|
|가로 막대형 차트    | 모든 숫자 열이 다른 계열(X 축)에 매핑되는 가로 막대형 차트를 표시합니다. 숫자 앞의 텍스트 열은 Y 축입니다(UI에서 제어할 수 있음).|
|누적 영역형 차트      | X 축이 첫 번째 열(숫자여야 함)인 누적 영역형 차트를 표시합니다. 모든 숫자 열은 다른 계열(Y 축)에 매핑됩니다. |
|타임라인 차트   | X 축이 첫 번째 열(날짜/시간이어야 함)인 시간 차트를 표시합니다. 모든 숫자 열은 다른 계열(Y 축)에 매핑됩니다.|
|꺾은선형 차트   | X 축이 첫 번째 열(숫자여야 함)인 꺾은선형 차트를 표시합니다. 모든 숫자 열은 다른 계열(Y 축)에 매핑됩니다.|
|[변칙 차트](#anomaly-chart)|    시간 차트와 비슷하지만, 기계 학습 변칙 알고리즘을 사용하여 시계열 데이터의 변칙을 찾습니다. 변칙 검색의 경우 Kusto.Explorer는 [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) 함수를 사용합니다.
|원형 차트    |    색 축이 첫 번째 열인 원형 차트를 표시합니다. 세타 축(백분율로 변환된 측정값이어야 함)은 두 번째 열입니다.|
|시간 사다리 |    X 축이 마지막 두 열(날짜/시간이어야 함)인 사다리 차트를 표시합니다. Y 축은 다른 열의 복합입니다.|
|분산형 차트| X 축이 첫 번째 열(숫자여야 함)인 점 그래프를 표시합니다. 모든 숫자 열은 다른 계열(Y 축)에 매핑됩니다.|
|피벗 차트  | 데이터, 열, 행 및 다양한 차트 종류를 유연하게 선택할 수 있는 피벗 테이블 및 피벗 차트를 표시합니다.| 
|시간 피벗   | 이벤트 타임라인에 대한 대화형 탐색입니다(시간 축에서 피벗).|

> [!NOTE]
> <a id="anomaly-chart">변칙 차트</a>: 알고리즘에는 다음 두 열로 구성된 시계열 데이터가 필요합니다.
>* 고정 간격 버킷의 시간
>* 변칙 검색을 위한 숫자 값. Kusto.Explorer에서 시계열 데이터를 생성하려면 시간 필드를 기준으로 요약하고 시간 버킷 bin을 지정합니다.

### <a name="view-section"></a>보기 섹션

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto Explorer 보기 메뉴":::

|메뉴           | 동작|
|---------------|---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨 작업 공간을 최대화합니다. **홈** > **전체 보기 모드** 를 차례로 선택하거나 **F11** 키를 눌러 전체 보기 모드를 종료합니다.|
|빈 열 숨기기| 데이터 그리드에서 빈 열을 제거합니다.|
|단수형 열 축소| 단수형 값을 사용하여 열을 축소합니다.|
|열 값 검색| 열 값 분포를 표시합니다.|
|글꼴 크기 늘이기  | 쿼리 탭 및 결과 데이터 그리드의 글꼴 크기를 늘립니다.|  
|글꼴 크기 줄이기  | 쿼리 탭 및 결과 데이터 그리드의 글꼴 크기를 줄입니다.|

>[!NOTE]
> 데이터 보기 설정:
>
> Kusto.Explorer는 고유한 열 세트별로 사용되는 설정을 추적합니다. 열을 다시 정렬하거나 제거하면 데이터 보기가 저장되고 동일한 열의 데이터를 검색할 때마다 다시 사용됩니다. 보기를 해당 기본값으로 다시 설정하려면 **보기** 탭에서 **보기 다시 설정** 을 선택합니다. 

## <a name="file-tab"></a>파일 탭

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto Explorer 파일 탭":::

|메뉴| 동작|
|---------------|---------|
||---------*쿼리 스크립트*---------|
|새 탭 | Kusto를 쿼리할 수 있는 새 탭 창을 엽니다. |
|파일 열기| 데이터를 *.kql 파일에서 활성 스크립트 패널로 로드합니다.|
|파일에 저장| 활성 스크립트 패널의 내용을 *.kql 파일에 저장합니다.|
|탭 닫기| 현재 탭 창을 닫습니다.|
||---------*데이터 저장*---------|
|데이터를 CSV로       | 데이터를 CSV(쉼표로 구분된 값) 파일로 내보냅니다.| 
|데이터를 JSON으로      | 데이터를 JSON 형식의 파일로 내보냅니다.|
|데이터를 Excel로     | 데이터를 XLSX(Excel) 파일로 내보냅니다.|
|데이터를 텍스트로      | 데이터를 TXT(텍스트) 파일로 내보냅니다.| 
|데이터를 KQL 스크립트로| 쿼리를 스크립트 파일로 내보냅니다.| 
|데이터를 결과로   | 쿼리 및 데이터를 결과(QRES) 파일로 내보냅니다.|
|CSV로 쿼리 실행 |쿼리를 실행하고 결과를 로컬 CSV 파일에 저장합니다.|
||---------*데이터 로드*---------|
|결과에서|    결과(QRES) 파일에서 쿼리 및 데이터를 로드합니다.| 
||---------*클립보드*---------|
|쿼리 및 결과를 클립보드로|    쿼리 및 데이터 세트를 클립보드로 내보냅니다. 차트가 표시되면 차트를 비트맵으로 내보냅니다.| 
|결과를 클립보드로| 데이터 세트를 클립보드로 내보냅니다. 차트가 표시되면 차트를 비트맵으로 내보냅니다.| 
|쿼리를 클립보드로| 쿼리를 클립보드로 내보냅니다.|
||---------*결과*---------|
|결과 캐시 지우기| 이전에 실행한 쿼리의 캐시된 결과를 지웁니다.| 

## <a name="connections-tab"></a>연결 탭

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto Explorer 연결 탭":::

|메뉴|동작|
|----|----------|
||---------*그룹*---------|
|그룹 추가| 새 Kusto 서버 그룹을 추가합니다.|
|그룹 이름 바꾸기| 기존 Kusto 서버 그룹의 이름을 바꿉니다.|
|그룹 제거| 기존 Kusto 서버 그룹을 제거합니다.|
||---------*클러스터*---------|
|연결 가져오기| 연결을 지정하는 파일에서 연결을 가져옵니다.|
|연결 내보내기| 연결을 파일로 내보냅니다.|
|연결 추가| 새 Kusto 서버 연결을 추가합니다.| 
|연결 편집| Kusto 서버 연결 속성 편집 대화 상자를 엽니다.|
|연결 제거| Kusto 서버에 대한 기존 연결을 제거합니다.|
|새로 고침| Kusto 서버 연결의 속성을 새로 고칩니다.|
||---------*보안*---------|
|ADD 보안 주체 검사| 현재 활성 사용자 세부 정보를 표시합니다.|
|AAD에서 로그아웃| AAD에 대한 연결에서 현재 사용자를 로그아웃합니다.|
||---------*데이터 범위*---------|
|캐싱 범위|<ul><li>핫 DataExecute는 [핫 데이터 캐시](../management/cachepolicy.md)에 대해서만 쿼리합니다.</li><li>모든 데이터: 사용 가능한 모든 데이터에 대해 쿼리를 실행합니다(기본값).</li></ul> |
|날짜/시간 열| 시간 사전 필터링에 사용할 수 있는 열의 이름입니다.|
|시간 필터링| 시간 사전 필터링 값입니다.|

## <a name="view-tab"></a>보기 탭

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto Explorer 보기 탭":::

|메뉴|동작|
|----|----------|
||---------*모양*---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨 작업 공간을 최대화합니다.|
|글꼴 크기 늘이기  | 쿼리 탭 및 결과 데이터 그리드의 글꼴 크기를 늘립니다.|  
|글꼴 크기 줄이기  | 쿼리 탭 및 결과 데이터 그리드의 글꼴 크기를 줄입니다.|
|레이아웃 다시 설정|도구의 도킹 컨트롤 및 창의 레이아웃을 다시 설정합니다.|
|문서 탭 이름 바꾸기 |선택한 탭의 이름을 바꿉니다. |
||---------*데이터 보기*---------|
|보기 다시 설정| [데이터 보기 설정](#dvs)을 해당 기본값으로 다시 설정합니다. |
|열 값 검색|열 값 분포를 표시합니다.|
|쿼리 통계에 포커스|쿼리 완료 시 쿼리 결과 대신 쿼리 통계로 포커스를 변경합니다.|
|중복 항목 숨기기|쿼리 결과에서 중복 행 제거를 설정/해제합니다.|
|빈 열 숨기기|쿼리 결과에서 빈 열 제거를 설정/해제합니다.|
|단수형 열 축소|단수형 값을 사용한 열 축소를 설정/해제합니다.|
||---------*데이터 필터링*---------|
|검색에서 행 필터링|쿼리 결과 검색에서 일치하는 행만 표시하는 옵션을 설정/해제합니다(**Ctrl+F**).|
||---------*시각화*---------|
|시각화|위의 [시각화](#visualizations-section)를 참조하세요. |

> [!NOTE]
> <a id="dvs">데이터 보기 설정:</a> 
>
> Kusto.Explorer는 고유한 열 세트별로 사용되는 설정을 추적합니다. 열을 다시 정렬하거나 제거하면 데이터 보기가 저장되고 동일한 열의 데이터를 검색할 때마다 다시 사용됩니다. 보기를 해당 기본값으로 다시 설정하려면 **보기** 탭에서 **보기 다시 설정** 을 선택합니다. 

## <a name="tools-tab"></a>도구 탭

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto Explorer 도구 탭":::

|메뉴|동작|
|----|----------|
||---------*IntelliSense*---------|
|IntelliSense 사용| 스크립트 패널에서 IntelliSense를 사용하거나 사용하지 않도록 설정합니다.|
||---------*분석*---------|
|쿼리 분석기| 쿼리 분석기 도구를 시작합니다.|
|쿼리 검사기 | 현재 쿼리를 분석하고 적용 가능한 개선 추천 사항 세트를 출력합니다.|
|계산기| 계산기를 시작합니다.|
||---------*분석*---------|
|분석 보고서| 데이터 분석을 위해 미리 작성된 여러 보고서가 포함된 대시보드를 엽니다.|
||---------*변환*---------|
|Power BI로 쿼리| 쿼리를 Power BI에서 사용하는 데 적합한 형식으로 변환합니다.|
||---------*옵션*---------|
|옵션 다시 설정| 애플리케이션 설정을 기본값으로 설정합니다.|
|옵션| 애플리케이션 설정을 구성하는 도구를 엽니다. [Kusto.Explorer 옵션](kusto-explorer-options.md)에 대해 자세히 알아보세요.|

## <a name="monitoring-tab"></a>모니터링 탭

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto Explorer 모니터링 탭":::

|메뉴             | 동작|
|-----------------|---------| 
||---------*모니터*---------|
|클러스터 진단 | 연결 패널에서 현재 선택된 서버 그룹에 대한 상태 요약을 표시합니다. | 
|최신 데이터: 모든 테이블| 현재 선택한 데이터베이스의 모든 테이블에 있는 최신 데이터의 요약을 표시합니다.|
|최신 데이터: 선택한 테이블|선택한 테이블의 최신 데이터를 상태 표시줄에 표시합니다.| 

## <a name="management-tab"></a>관리 탭

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto Explorer 관리 탭":::

|메뉴             | 동작|
|-----------------|---------|
||---------*권한 있는 보안 주체*---------|
|권한 있는 클러스터 보안 주체 관리 |권한 있는 사용자에 대한 클러스터의 보안 주체를 관리할 수 있습니다.| 
|권한 있는 데이터베이스 보안 주체 관리 | 권한 있는 사용자에 대한 데이터베이스 보안 주체를 관리할 수 있습니다.| 
|권한 있는 테이블 보안 주체 관리 | 권한 있는 사용자에 대한 테이블 보안 주체를 관리할 수 있습니다.| 
|권한 있는 함수 보안 주체 관리 | 권한 있는 사용자에 대한 함수 보안 주체를 관리할 수 있습니다.| 

## <a name="help-tab"></a>도움말 탭

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto Explorer 도움말 탭":::

|메뉴             | 동작|
|-----------------|---------|
||---------*문서화*---------|
|도움말             | Kusto 온라인 설명서에 대한 링크를 엽니다.  | 
|새로운 기능       | 모든 Kusto.Explorer 변경 내용을 나열하는 문서를 엽니다.|
|보고서 문제      |다음 두 가지 옵션이 있는 대화 상자를 엽니다. <ul><li>서비스와 관련된 문제 보고</li><li>클라이언트 애플리케이션의 문제 보고</li></ul> | 
|기능 제안  | Kusto 피드백 포럼에 대한 링크를 엽니다. | 
|업데이트 확인     | Kusto.Explorer 버전에 대한 업데이트가 있는지 확인합니다. | 

## <a name="connections-panel"></a>연결 패널

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto Explorer 연결 패널":::

[연결] 창에는 구성된 클러스터 연결이 모두 표시됩니다. 각 클러스터에 대해 저장한 데이터베이스, 테이블 및 특성(열)이 표시됩니다. 기본 패널에서 검색/쿼리에 대한 암시적 컨텍스트를 설정하는 항목을 선택하거나 항목을 두 번 클릭하여 이름을 검색/쿼리 패널에 복사합니다.

실제 스키마가 큰 경우(예: 수백 개의 테이블이 있는 데이터베이스) **CTRL+F** 를 누르고 찾고 있는 엔터티 이름의 하위 문자열(대/소문자 구분 안 함)을 입력하여 검색할 수 있습니다.

Kusto.Explorer는 쿼리 창에서 연결 패널을 제어할 수 있도록 지원하므로 스크립트에 유용합니다. 예를 들어 다음 구문을 사용하여 스크립트에서 쿼리하는 데이터가 있는 클러스터/데이터베이스에 연결하도록 Kusto.Explorer에 지시하는 명령을 사용하여 스크립트 파일을 시작할 수 있습니다.

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

`F5` 키 또는 비슷한 항목을 사용하여 각 줄을 실행합니다.

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Kusto.Explorer에 연결하는 사용자 ID 제어

새 연결에 대한 기본 보안 모델은 AAD 페더레이션 보안입니다. 인증은 기본 AAD 사용자 환경을 사용하여 Azure Active Directory를 통해 수행됩니다.

인증 매개 변수를 더 세밀하게 제어해야 하는 경우 "고급: 연결 문자열" 편집 상자를 펼쳐서 유효한 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 값을 제공합니다.

예를 들어 여러 AAD 테넌트에 있는 사용자는 특정 AAD 테넌트에 대한 자신의 ID의 특정 "프로젝션"을 사용해야 하는 경우가 있습니다. 이 작업은 아래와 같은 연결 문자열을 제공하여 수행합니다(대문자로 된 단어를 특정 값으로 바꿈).

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`는 클러스터가 호스팅되는 AAD 테넌트의 도메인 이름 또는 AAD 테넌트 ID(GUID)입니다. 이는 일반적으로 클러스터를 소유하는 조직의 도메인 이름입니다(예: `contoso.com`). 
* USER_DOMAIN은 해당 테넌트에 초대된 사용자의 ID입니다(예: `user@example.com`). 

>[!NOTE]
> 사용자의 도메인 이름은 클러스터를 호스팅하는 테넌트의 도메인 이름과 반드시 같을 필요는 없습니다.

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto Explorer 고급 연결 문자열":::

## <a name="keyboard-shortcuts"></a>바로 가기 키

바로 가기 키를 사용하면 마우스를 사용하는 것보다 더 빠르게 작업을 수행할 수 있습니다. 자세한 내용은 이 [Kusto.Explorer 바로 가기 키 목록](kusto-explorer-shortcuts.md)을 살펴보세요.

## <a name="table-row-colors"></a>테이블 행 색

Kusto.Explorer는 결과 패널에서 각 행의 심각도 또는 세부 정보 표시 수준을 해석하고 이에 따라 색을 지정합니다. 이렇게 하려면 각 열의 고유 값을 알려진 패턴 세트("경고", "오류" 등)와 일치시킵니다.

출력 색 구성표를 수정하거나 이 동작을 해제하려면 **도구** 메뉴에서 **옵션** > **결과 뷰어** > **색 구성표 세부 정보 표시** 를 차례로 선택합니다.

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto Explorer 색 구성표 수정":::

## <a name="next-steps"></a>다음 단계

Kusto.Explorer를 사용하는 방법에 대해 자세히 알아봅니다.

* [Kusto.Explorer 사용](kusto-explorer-using.md)
* [Kusto.Explorer 바로 가기 키](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 옵션](kusto-explorer-options.md)
* [Kusto.Explorer 문제 해결](kusto-explorer-troubleshooting.md)

Kusto.Explorer 도구 및 유틸리티에 대해 자세히 알아봅니다.
* [Kusto.Explorer 코드 분석기](kusto-explorer-code-analyzer.md)
* [Kusto.Explorer 코드 탐색](kusto-explorer-codenav.md)
* [Kusto.Explorer 코드 리팩터링](kusto-explorer-refactor.md)
* [Kusto Query Language(KQL)](/azure/kusto/query/)