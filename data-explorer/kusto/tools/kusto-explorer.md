---
title: Kusto 탐색기 시작
description: Kusto 탐색기의 기능 및 데이터를 탐색 하는 데 도움이 되는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 15c9ff61067f25f8a0f63ce4078b158277740db3
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863561"
---
# <a name="getting-started-with-kustoexplorer"></a>Kusto 탐색기 시작

Kusto. 탐색기는 사용 하기 쉬운 사용자 인터페이스에서 Kusto 쿼리 언어를 사용 하 여 데이터를 탐색할 수 있도록 하는 풍부한 데스크톱 응용 프로그램입니다. 이 개요에서는 Kusto 탐색기를 설정 하는 방법을 설명 하 고 사용할 사용자 인터페이스에 대해 설명 합니다.

Kusto 탐색기를 사용 하 여 다음을 수행할 수 있습니다.
* [데이터를 쿼리](kusto-explorer-using.md#query-mode)합니다.
* 테이블에서 [데이터를 검색](kusto-explorer-using.md#search-mode) 합니다.
* 다양 한 그래프에서 [데이터를 시각화](#visualizations-section) 합니다.
* 전자 메일 또는 딥 링크를 사용 하 여 [쿼리 및 결과를 공유](kusto-explorer-using.md#share-queries-and-results) 합니다.

## <a name="installing-kustoexplorer"></a>Kusto 탐색기 설치

* [Kusto 탐색기 도구를 설치 합니다.](https://aka.ms/ke)

* 대신에 브라우저를 사용 하 여 Kusto 클러스터에 액세스 [합니다. https://<your_cluster>. kusto.windows.net](https://your_cluster.kusto.windows.net). <your_cluster>를 Azure 데이터 탐색기 클러스터 이름으로 바꿉니다.

### <a name="using-chrome-and-kustoexplorer"></a>Chrome 및 Kusto 탐색기 사용

Chrome을 기본 브라우저로 사용 하는 경우 Chrome에 대 한 ClickOnce 확장을 설치 해야 합니다.

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>사용자 인터페이스 개요

Kusto 탐색기 사용자 인터페이스는 다른 Microsoft 제품의 경우와 유사 하 게 탭과 패널을 기반으로 하는 레이아웃을 사용 하 여 디자인 되었습니다. 

1. [메뉴 패널](#menu-panel) 의 탭을 탐색 하 여 다양 한 작업 수행
2. [연결 패널](#connections-panel) 에서 연결 관리
3. 스크립트 패널에서 실행할 스크립트 만들기
4. 결과 패널에서 스크립트의 결과를 확인 합니다.

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto Explorer 시작":::

## <a name="menu-panel"></a>메뉴 패널

Kusto 탐색기 메뉴 패널에는 다음과 같은 탭이 있습니다.

* [홈](#home-tab)
* [파일](#file-tab)
* [연결](#connections-tab)
* [보기](#view-tab)
* [Tools](#tools-tab)
* [Monitoring](#monitoring-tab)
* [관리](#management-tab)
* [도움말](#help-tab)

### <a name="home-tab"></a>홈 탭

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto 탐색기 홈 탭":::

홈 탭은 가장 최근에 사용한 함수를 섹션으로 구분 하 여 보여 줍니다.

* [쿼리](#query-section)
* [공유](#share-section)
* [시각화](#visualizations-section)
* [보기](#view-section)
* [도움말](#help-tab) 

### <a name="query-section"></a>쿼리 섹션

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="쿼리 메뉴 Kusto Explorer":::

|메뉴|    동작|
|----|----------|
|모드 드롭다운 | <ul><li>쿼리 모드: 쿼리 창을 [스크립트 모드로](kusto-explorer-using.md#query-mode)전환 합니다. 명령을 로드 하 여 스크립트로 저장할 수 있습니다 (기본값).</li> <li> 검색 모드: 입력 한 각 명령이 즉시 처리 되 고 결과 창에 결과가 표시 되는 단일 쿼리 모드입니다.</li> <li>검색 + + 모드: 하나 이상의 테이블에서 검색 구문을 사용 하 여 용어를 검색할 수 있습니다. [Search + + 모드](kusto-explorer-using.md#search-mode) 사용에 대 한 자세한 정보</li></ul> |
|새 탭| Kusto 쿼리를 위한 새 탭을 엽니다. |

### <a name="share-section"></a>섹션 공유

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto 탐색기 공유 메뉴":::

|메뉴|    동작|
|----|----------|
|데이터를 클립보드에 복사|    쿼리 및 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 결과 반환| 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 쿼리| 쿼리를 클립보드로 내보냅니다.|

### <a name="visualizations-section"></a>시각화 섹션

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto Explorer 메뉴 시각화":::

|메뉴         | 동작|
|-------------|---------|
|영역형 차트      | X 축이 첫 번째 열 (숫자 여야 함) 인 영역형 차트를 표시 합니다. 모든 숫자 열이 다른 계열 (Y 축)에 매핑됩니다. |
|세로 막대형 차트 | 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 세로 막대형 차트를 표시 합니다. 숫자 앞에 있는 텍스트 열은 X 축 (UI에서 제어 가능)입니다.|
|가로 막대형 차트    | 모든 숫자 열이 다른 계열 (X 축)에 매핑되는 가로 막대형 차트를 표시 합니다. 숫자 앞에 있는 텍스트 열은 Y 축 (UI에서 제어할 수 있음)입니다.|
|누적 영역형 차트      | X 축이 첫 번째 열 (숫자 여야 함) 인 누적 영역형 차트를 표시 합니다. 모든 숫자 열이 다른 계열 (Y 축)에 매핑됩니다. |
|타임라인 차트   | X 축이 첫 번째 열인 시간 차트를 표시 합니다 (datetime 이어야 함). 모든 숫자 열이 다른 계열 (Y 축)에 매핑됩니다.|
|꺾은선형 차트   | X 축이 첫 번째 열 (숫자 여야 함) 인 꺾은선형 차트를 표시 합니다. 모든 숫자 열이 다른 계열 (Y 축)에 매핑됩니다.|
|변칙 차트|    시간 차트와 유사 하지만 기계 학습 이상 알고리즘을 사용 하 여 시계열 데이터의 변칙을 찾습니다. 변칙 검색의 경우, Kusto 탐색기는 [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) 함수를 사용 합니다. (*) 
|원형 차트    |    색 축이 첫 번째 열인 원형 차트를 표시 합니다. 테타 축 (백분율로 변환 된 측정값이 되어야 함)은 두 번째 열입니다.|
|시간 사다리 |    X 축이 마지막 두 열 (datetime 이어야 함) 인 사다리 차트를 표시 합니다. Y 축은 다른 열의 복합입니다.|
|분산형 차트| X 축이 첫 번째 열 (숫자 여야 함) 인 점 그래프를 표시 합니다. 모든 숫자 열이 다른 계열 (Y 축)에 매핑됩니다.|
|피벗 차트  | 데이터, 열, 행 및 다양 한 차트 종류를 유연 하 게 선택할 수 있는 피벗 테이블 및 피벗 차트를 표시 합니다.| 
|시간 피벗   | 이벤트 시간 행에 대 한 대화형 탐색 (시간 축에서 피벗)|

(*) 변칙 차트: 알고리즘은 다음 두 열로 구성 된 시계열 데이터를 필요로 합니다.
1. 고정 간격 버킷의 시간
2. 변칙 검색을 위한 숫자 값으로, Kusto. 탐색기에서 시계열 데이터를 생성 하 고, 시간 필드를 기준으로 요약 하 고, 시간 버킷 bin을 지정 합니다.

### <a name="view-section"></a>섹션 보기

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto 탐색기 보기 메뉴":::

|메뉴           | 동작|
|---------------|---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨서 작업 공간을 최대화 합니다. **홈**  >  **전체 보기 모드**를 선택 하거나 **F11**키를 눌러 전체 보기 모드를 종료 합니다.|
|빈 열 숨기기| 데이터 표에서 빈 열을 제거 합니다.|
|단일 열 축소| 값이 단수형 인 열 축소|
|열 값 탐색| 열 값 분포 표시|
|글꼴 늘리기  | 쿼리 탭의 글꼴 크기와 결과 데이터 표를 늘립니다.|  
|글꼴 줄이기  | 쿼리 탭 및 결과 데이터 표의 글꼴 크기를 줄입니다.|

(*) 데이터 보기 설정: Kusto 탐색기는 고유 열 집합 마다 사용 되는 설정을 추적 합니다. 따라서 열을 다시 정렬 하거나 제거 하면 데이터 뷰가 저장 되 고 동일한 열이 있는 데이터가 검색 될 때마다 다시 사용 됩니다. 뷰를 기본값으로 다시 설정 하려면 **보기** 탭에서 **보기 다시 설정**을 선택 합니다. 

## <a name="file-tab"></a>파일 탭

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto 탐색기 파일 탭":::

|메뉴| 동작|
|---------------|---------|
||---------*쿼리 스크립트*---------|
|새 탭 | Kusto 쿼리를 위한 새 탭 창을 엽니다. |
|파일 열기| Kql 파일의 데이터를 활성 스크립트 패널로 로드 합니다.|
|파일에 저장| 활성 스크립트 패널의 내용을 *. kql 파일에 저장 합니다.|
|탭 닫기| 현재 탭 창을 닫습니다.|
||---------*데이터 저장*---------|
|데이터를 CSV로       | CSV (쉼표로 구분 된 값) 파일로 데이터를 내보냅니다.| 
|데이터를 JSON으로      | JSON 형식 파일로 데이터를 내보냅니다.|
|데이터를 Excel로     | 데이터를 .XLSX (Excel) 파일로 내보냅니다.|
|데이터를 텍스트로      | TXT (텍스트) 파일로 데이터를 내보냅니다.| 
|KQL 스크립트에 대 한 데이터| 스크립트 파일에 쿼리를 내보냅니다.| 
|결과에 대 한 데이터   | 쿼리 및 데이터를 결과 (QRES) 파일로 내보냅니다.|
|CSV로 쿼리 실행 |쿼리를 실행 하 고 결과를 로컬 CSV 파일에 저장 합니다.|
||---------*데이터 로드*---------|
|결과에서|    결과 (QRES) 파일에서 쿼리 및 데이터를 로드 합니다.| 
||---------*클립보드*---------|
|쿼리 및 클립보드로 결과 쿼리|    쿼리 및 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 결과 반환| 클립보드에 설정 된 데이터를 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 쿼리| 쿼리를 클립보드로 내보냅니다.|
||---------*검색*---------|
|결과 캐시 지우기| 이전에 실행 한 쿼리의 캐시 된 결과를 지웁니다.| 

## <a name="connections-tab"></a>연결 탭

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto Explorer 연결 탭":::

|메뉴|동작|
|----|----------|
||---------*그룹과*---------|
|그룹 추가| 새 Kusto 서버 그룹을 추가 합니다.|
|그룹 이름 바꾸기| 기존 Kusto 서버 그룹의 이름을 바꿉니다.|
|그룹 제거| 기존 Kusto 서버 그룹을 제거 합니다.|
||---------*클러스터*---------|
|연결 가져오기| 연결을 지정 하는 파일에서 연결을 가져옵니다.|
|연결 내보내기| 파일에 대 한 연결 내보내기|
|연결 추가| 새 Kusto 서버 연결을 추가 합니다.| 
|연결 편집| Kusto 서버 연결 속성 편집 대화 상자를 엽니다.|
|연결 제거| Kusto 서버에 대 한 기존 연결을 제거 합니다.|
|새로 고침| Kusto 서버 연결의 속성을 새로 고칩니다.|
||---------*보안*---------|
|보안 주체를 검사 합니다.| Currents 활성 사용자 정보를 표시 합니다.|
|AAD에서 로그 아웃| AAD에 대 한 연결에서 현재 사용자를 로그 아웃 합니다.|
||---------*데이터 범위*---------|
|캐싱 범위|<ul><li>핫 DataExecute는 [핫 데이터 캐시](../management/cachepolicy.md) 에서만 쿼리를 실행 합니다.</li><li>모든 데이터: 사용 가능한 모든 데이터에 대해 쿼리 실행 (기본값)</li></ul> |
|DateTime 열| 시간 사전 필터에 사용할 수 있는 열의 이름입니다.|
|시간 필터| 시간 사전 필터 값|

## <a name="view-tab"></a>보기 탭

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto 탐색기 보기 탭":::

|메뉴|동작|
|----|----------|
||---------*바꿀*---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨서 작업 공간을 최대화 합니다.|
|글꼴 늘리기  | 쿼리 탭의 글꼴 크기와 결과 데이터 표를 늘립니다.|  
|글꼴 줄이기  | 쿼리 탭 및 결과 데이터 표의 글꼴 크기를 줄입니다.|
|레이아웃 다시 설정|도구의 도킹 컨트롤과 창의 레이아웃을 다시 설정 합니다.|
|문서 탭 이름 바꾸기 |선택한 탭의 이름 바꾸기 |
||---------*데이터 뷰*---------|
|보기 다시 설정| 데이터 뷰 설정을 기본값으로 다시 설정 합니다. (*)|
|열 값 탐색|열 값 분포 표시|
|쿼리 통계에 집중|쿼리가 완료 될 때 쿼리 결과가 아닌 쿼리 통계로 포커스를 변경 합니다.|
|중복 항목 숨기기|쿼리 결과에서 중복 행 제거를 설정/해제 합니다.|
|빈 열 숨기기|쿼리 결과에서 빈 열의 제거를 설정/해제 합니다.|
|단일 열 축소|값이 단수형 인 열 축소/축소|
||---------*데이터 필터링*---------|
|검색에서 행 필터링|쿼리 결과 검색에서 일치 하는 행만 표시 옵션을 설정/해제 합니다 (**Ctrl + F**).|
||---------*가상화*---------|
|시각화|위의 [시각화](#visualizations-section)를 참조 하세요. |

(*) 데이터 보기 설정: Kusto 탐색기는 고유 열 집합에 따라 사용 되는 설정을 추적 합니다. 열을 다시 정렬 하거나 제거 하면 데이터 뷰가 저장 되 고 동일한 열이 있는 데이터가 검색 될 때마다 다시 사용 됩니다. 뷰를 기본값으로 다시 설정 하려면 **보기** 탭에서 **보기 다시 설정**을 선택 합니다. 

## <a name="tools-tab"></a>도구 탭

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto 탐색기 도구 탭":::

|메뉴|동작|
|----|----------|
||---------*메서드가*---------|
|IntelliSense 사용| 스크립트 패널에서 IntelliSense를 사용 하거나 사용 하지 않도록 설정 합니다.|
||---------*분석*---------|
|쿼리 분석기| Query Analyzer 도구를 시작 합니다.|
|쿼리 검사기 | 현재 쿼리를 분석 하 고 적용 가능한 개선 권장 사항의 집합을 출력 합니다.|
|계산기| 계산기를 시작 합니다.|
||---------*Analytics*---------|
|분석 보고서| 데이터 분석을 위해 미리 작성 된 여러 보고서를 사용 하 여 대시보드를 엽니다.|
||---------*번역하기*---------|
|Power BI에 대 한 쿼리| 에서를 사용 하는 데 적합 한 형식으로 쿼리를 변환 Power BI|
||---------*옵션*---------|
|옵션 다시 설정| 응용 프로그램 설정을 기본값으로 설정 합니다.|
|옵션| 응용 프로그램 설정을 구성 하는 도구를 엽니다. [Kusto 탐색기 옵션](kusto-explorer-options.md)에 대해 자세히 알아보세요.|

## <a name="monitoring-tab"></a>모니터링 탭

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto 탐색기 모니터링 탭":::

|메뉴             | 동작|
|-----------------|---------| 
||---------*감시*---------|
|클러스터 진단 | 연결 패널에서 현재 선택 된 서버 그룹에 대 한 상태 요약 정보를 표시 합니다. | 
|최신 데이터: 모든 테이블| 현재 선택한 데이터베이스의 모든 테이블에 있는 최신 데이터의 요약 정보를 표시 합니다.|
|최신 데이터: 선택한 테이블|상태 표시줄에서 선택한 테이블의 최신 데이터를 표시 합니다.| 

## <a name="management-tab"></a>관리 탭

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto 탐색기 관리 탭":::

|메뉴             | 동작|
|-----------------|---------|
||---------*권한 있는 보안 주체*---------|
|클러스터 인증 된 보안 주체 관리 |권한 있는 사용자에 대 한 클러스터의 보안 주체를 관리할 수 있습니다.| 
|데이터베이스 인증 된 보안 주체 관리 | 권한 있는 사용자에 대 한 데이터베이스 보안 주체를 관리할 수 있습니다.| 
|테이블 인증 된 보안 주체 관리 | 권한 있는 사용자에 대해 테이블의 보안 주체를 관리할 수 있습니다.| 
|함수 인증 된 보안 주체 관리 | 권한 있는 사용자에 대 한 함수의 보안 주체를 관리할 수 있습니다.| 

## <a name="help-tab"></a>도움말 탭

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto 탐색기 도움말 탭":::

|메뉴             | 동작|
|-----------------|---------|
||---------*설명을*---------|
|도움말             | Kusto 온라인 설명서에 대 한 링크를 엽니다.  | 
|새로운 기능       | 모든 Kusto 탐색기 변경 내용을 나열 하는 문서를 엽니다.|
|보고서 문제      |두 가지 옵션이 있는 대화 상자를 엽니다. <ul><li>서비스와 관련 된 문제 보고</li><li>클라이언트 응용 프로그램의 문제 보고</li></ul> | 
|제안 기능  | Kusto 피드백 포럼에 대 한 링크를 엽니다. | 
|업데이트 확인     | Kusto 탐색기 버전에 대 한 업데이트가 있는지 확인 합니다. | 

## <a name="connections-panel"></a>연결 패널

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto Explorer 연결 패널":::

연결 창에는 구성 된 모든 클러스터 연결이 표시 됩니다. 각 클러스터에 대해 저장 된 데이터베이스, 테이블 및 특성 (열)이 표시 됩니다. 항목을 선택 (주 패널의 검색/쿼리에 대 한 암시적 컨텍스트를 설정) 하거나 항목을 두 번 클릭 하 여 검색/쿼리 패널에 이름을 복사 합니다.

실제 스키마가 많은 경우 (예: 수백 개의 테이블이 있는 데이터베이스) **CTRL + F** 를 눌러 검색 하 고 찾으려는 엔터티 이름에 대/소문자를 구분 하지 않는 부분 문자열을 입력 하 여 검색할 수 있습니다.

Kusto 탐색기는 스크립트에 유용한 쿼리 창에서 연결 패널의 제어를 지원 합니다. 예를 들어 다음과 같은 구문을 사용 하 여 스크립트에서 데이터를 쿼리 하는 클러스터/데이터베이스에 연결 하는 명령을 사용 하 여 스크립트 파일을 시작할 수 있습니다.

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

또는 이와 비슷한를 사용 하 여 각 줄 `F5` 을 실행 합니다.

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Kusto. 탐색기에 연결 하는 사용자 id를 제어 합니다.

새 연결에 대 한 기본 보안 모델은 AAD 페더레이션 보안입니다. 인증은 기본 AAD 사용자 환경을 사용 하 여 Azure Active Directory를 통해 수행 됩니다.

인증 매개 변수를 보다 세밀 하 게 제어 해야 하는 경우 "고급: 연결 문자열" 편집 상자를 확장 하 고 유효한 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 값을 제공할 수 있습니다.

예를 들어, 여러 AAD 테 넌 트에 있는 사용자는 때때로 특정 AAD 테 넌 트에 id의 특정 "프로젝션"을 사용 해야 합니다. 아래와 같은 연결 문자열을 제공 하 여이 작업을 수행 합니다 (대문자로 된 단어를 특정 값으로 대체).

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`클러스터가 호스트 되는 AAD 테 넌 트의 도메인 이름 또는 AAD 테 넌 트 ID (GUID)입니다. 이는 일반적으로 클러스터를 소유 하는 조직의 도메인 이름입니다 (예:) `contoso.com` . 
* USER_DOMAIN는 해당 테 넌 트에 초대 된 사용자의 id (예: `user@example.com` )입니다. 

>[!Note]
> 사용자의 도메인 이름은 클러스터를 호스트 하는 테 넌 트의 도메인 이름과 같을 필요는 없습니다.

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto 탐색기 고급 연결 문자열":::

## <a name="keyboard-shortcuts"></a>바로 가기 키

바로 가기 키를 사용 하면 마우스를 사용 하는 것 보다 빠르게 작업을 수행할 수 있습니다. 자세한 내용은 [Kusto 탐색기 바로 가기 키 목록을](kusto-explorer-shortcuts.md) 살펴보세요.

## <a name="table-row-colors"></a>테이블 행 색

Kusto 탐색기는 결과 패널에서 각 행의 심각도 또는 세부 정보 표시 수준을 해석 하 고 그에 따라 색을 표시 하려고 합니다. 이렇게 하려면 각 열의 고유 값을 알려진 패턴 집합 ("경고", "오류" 등)과 일치 합니다.

출력 색 구성표를 수정 하거나이 동작을 해제 하려면 **도구** 메뉴에서 **옵션**  >  **결과 뷰어**  >  **자세한 정도 색 구성표**를 선택 합니다.

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto 탐색기 색 구성표 수정":::

## <a name="next-steps"></a>다음 단계

Kusto 탐색기를 사용 하는 방법에 대해 자세히 알아보세요.

* [Kusto 탐색기 사용](kusto-explorer-using.md)
* [Kusto 탐색기 바로 가기 키](kusto-explorer-shortcuts.md)
* [Kusto. 탐색기 옵션](kusto-explorer-options.md)
* [Kusto 탐색기 문제 해결](kusto-explorer-troubleshooting.md)

Kusto 탐색기 도구 및 유틸리티에 대해 자세히 알아보세요.
* [Kusto 탐색기 코드 분석기](kusto-explorer-code-analyzer.md)
* [Kusto 탐색기 코드 탐색](kusto-explorer-codenav.md)
* [Kusto 탐색기 코드 리팩터링](kusto-explorer-refactor.md)
* [Kusto 쿼리 언어 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
