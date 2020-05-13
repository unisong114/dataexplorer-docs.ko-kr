---
title: Kusto 탐색기 도구-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 탐색기 도구에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 60a414ff871d88de041e8b76671b73d98854fba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374063"
---
# <a name="kustoexplorer-tool"></a>Kusto 탐색기 도구

Kusto. 탐색기는 Kusto 쿼리 언어를 사용 하 여 데이터를 탐색할 수 있는 풍부한 데스크톱 응용 프로그램입니다.

## <a name="getting-the-tool"></a>도구 가져오기

* [Kusto 탐색기 도구를 설치 합니다.](https://aka.ms/Kusto.Explorer)

* 또는에서 브라우저를 사용 하 여 Kusto 클러스터에 액세스 `https://<your_cluster>.kusto.windows.net` 합니다. <your_cluster>를 Azure 데이터 탐색기 클러스터 이름으로 바꿉니다.



## <a name="using-chrome-and-kustoexplorer"></a>Chrome 및 Kusto 탐색기 사용

Chrome을 기본 브라우저로 사용 하는 경우 Chrome에 대 한 ClickOnce 확장을 설치 해야 합니다.

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)



## <a name="overview-of-the-user-experience"></a>사용자 환경 개요

Kusto Explorer 창에는 몇 가지 UI 부분이 있습니다.

1. [메뉴 패널](#menu-panel)
2. [연결 패널](#connections-panel)
3. 스크립트 패널
4. 결과 패널

![Kusto 탐색기 시작](./Images/KustoTools-KustoExplorer/ke-start.png "ke-시작")

### <a name="keyboard-shortcuts"></a>바로 가기 키

바로 가기 키를 사용 하면 마우스를 사용 하는 것 보다 빠르게 작업을 수행할 수 있습니다. [Kusto 탐색기 바로 가기 키 목록을](kusto-explorer-shortcuts.md)살펴보세요.

### <a name="menu-panel"></a>메뉴 패널

Kusto 탐색기 메뉴 패널에는 다음과 같은 탭이 있습니다.

* [홈](#home-tab)
* [최근에 사용한 파일](#file-tab)
* [연결](#connections-tab)
* [보기](#view-tab)
* [도구](#tools-tab)

* [관리](#management-tab)
* [도움말](#help-tab)

### <a name="home-tab"></a>홈 탭

![Kusto 탐색기 홈](./Images/KustoTools-KustoExplorer/home-tab.png "home-tab")

홈 탭은 가장 최근에 사용한 함수를 섹션으로 구분 하 여 보여 줍니다.

* [쿼리](#query-section)
* [공유](#share-section)
* [시각화](#visualizations-section)
* [보기](#view-section)
* [도움말](#help-tab) 

#### <a name="query-section"></a>쿼리 섹션

![Kusto 탐색기 쿼리 메뉴](./Images/KustoTools-KustoExplorer/home-query-menu.png "쿼리-메뉴")

|메뉴|    동작|
|----|----------|
|모드 드롭다운 | <ul><li>쿼리 모드: 쿼리 창을 [스크립트 모드로](#query-mode)전환 합니다. 명령을 로드 하 여 스크립트로 저장할 수 있습니다 (기본값).</li> <li> 검색 모드: 입력 한 각 명령이 즉시 처리 되 고 결과 창에 결과가 표시 되는 단일 쿼리 모드입니다.</li> <li>검색 + + 모드: 하나 이상의 테이블에서 검색 구문을 사용 하 여 용어를 검색할 수 있습니다. [Search + + 모드](kusto-explorer.md#search-mode) 사용에 대 한 자세한 정보</li></ul> |
|새 탭| Kusto 쿼리를 위한 새 탭을 엽니다. |

#### <a name="share-section"></a>섹션 공유

![Kusto 탐색기 공유 섹션](./Images/KustoTools-KustoExplorer/home-share-menu.png "공유-메뉴")

|메뉴|    동작|
|----|----------|
|데이터를 클립보드에 복사|    쿼리 및 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 결과 반환| 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 쿼리| 쿼리를 클립보드로 내보냅니다.|

#### <a name="visualizations-section"></a>시각화 섹션

![대체 텍스트](./Images/KustoTools-KustoExplorer/home-visualizations-menu.png "메뉴-시각화")

|메뉴         | 동작|
|-------------|---------|
|영역형 차트      | X 축이 첫 번째 열 (숫자 여야 함)이 고 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 영역형 차트를 표시 합니다. |
|세로 막대형 차트 | 모든 숫자 열이 다른 계열 (Y 축)에 매핑되고 숫자 앞에 있는 텍스트 열이 X 축 (UI에서 제어 가능) 인 세로 막대형 차트를 표시 합니다.|
|가로 막대형 차트    | 모든 숫자 열이 다른 계열 (X 축)에 매핑되고 숫자 앞에 있는 텍스트 열이 Y 축 (UI에서 제어 가능) 인 가로 막대형 차트를 표시 합니다.|
|누적 영역형 차트      | X 축이 첫 번째 열 (숫자 여야 함)이 고 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 누적 영역형 차트를 표시 합니다. |
|타임라인 차트   | X 축이 첫 번째 열 (datetime 이어야 함)이 고 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 시간 차트를 표시 합니다.|
|꺾은선형 차트   | X 축이 첫 번째 열 (숫자 여야 함)이 고 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 꺾은선형 차트를 표시 합니다.|
|변칙 차트|    시간 차트와 유사 하지만 기계 학습 이상 알고리즘을 사용 하 여 시계열 데이터의 변칙을 찾습니다. 변칙 검색의 경우, Kusto 탐색기는 [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) 함수를 사용 합니다. (*) 
|원형 차트    |    색 축이 첫 번째 열이 고 테타 축 (백분율로 변환 된 측정값이 되어야 함)이 두 번째 열인 원형 차트를 표시 합니다.|
|사다리 차트 |    X 축이 마지막 두 열 (datetime 이어야 함)이 고 Y 축이 다른 열의 복합 인 사다리 차트를 표시 합니다.|
|분산형 차트| X 축이 첫 번째 열 (숫자 여야 함)이 고 모든 숫자 열이 다른 계열 (Y 축)에 매핑되는 점 그래프를 표시 합니다.|
|피벗 차트  | 데이터, 열, 행 및 다양 한 차트 종류를 유연 하 게 선택할 수 있는 피벗 테이블 및 피벗 차트를 만듭니다.| 
|시간 피벗   | 이벤트 시간 행에 대 한 대화형 탐색 (시간 축에서 피벗)|

(*) 변칙 차트: 알고리즘은 다음 두 열로 구성 된 시계열 데이터를 필요로 합니다.
1. 고정 간격 버킷의 시간
2. 변칙 검색을 위한 숫자 값으로, Kusto. 탐색기에서이를 생성 하 고 시간 필드를 기준으로 요약 하며 시간 버킷 bin을 지정 합니다.

#### <a name="view-section"></a>섹션 보기

![대체 텍스트](./Images/KustoTools-KustoExplorer/home-view-menu.png "보기-메뉴")

|메뉴           | 동작|
|---------------|---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨서 작업 공간을 최대화 합니다.|
|빈 열 숨기기| 데이터 표에서 빈 열을 제거 합니다.|
|단일 열 축소| 값이 단수형 인 열 축소|
|열 값 탐색| 열 값 분포 표시|
|글꼴 늘리기  | 쿼리 탭의 글꼴 크기와 결과 데이터 표를 늘립니다.|  
|글꼴 줄이기  | 쿼리 탭 및 결과 데이터 표의 글꼴 크기를 줄입니다.|

(*) 데이터 보기 설정: Kusto 탐색기는 고유 열 집합 마다 사용 되는 설정을 추적 합니다. 따라서 열을 다시 정렬 하거나 제거 하면 데이터 뷰가 저장 되 고 동일한 열이 있는 데이터가 검색 될 때마다 다시 사용 됩니다. 뷰를 기본값으로 다시 설정 하려면 **보기** 탭에서 **보기 다시 설정**을 선택 합니다. 

### <a name="file-tab"></a>파일 탭

![Kusto 탐색기 파일](./Images/KustoTools-KustoExplorer/file-tab.png "파일-탭")

|메뉴| 동작|
|---------------|---------|
||---------*쿼리 스크립트*---------|
|새 탭 | Kusto 쿼리를 위한 새 탭 창을 엽니다. |
|파일 열기| Kql 파일의 데이터를 활성 스크립트 패널로 로드 합니다.|
|파일에 저장| 활성 스크립트 패널의 내용을 *. kql 파일에 저장 합니다.|
|탭 닫기| 현재 탭 창을 닫습니다.|
||---------*데이터 저장*---------|
|CSV로       | CSV (쉼표로 구분 된 값) 파일로 데이터를 내보냅니다.| 
|JSON으로      | JSON 형식 파일로 데이터를 내보냅니다.|
|Excel로     | 데이터를 .XLSX (Excel) 파일로 내보냅니다.|
|텍스트로      |    TXT (텍스트) 파일로 데이터를 내보냅니다.| 
|CSL 스크립트로|    스크립트 파일에 쿼리를 내보냅니다.| 
|결과에   |    쿼리 및 데이터를 결과 (QRES) 파일로 내보냅니다.|
||---------*데이터 로드*---------|
|결과에서|    결과 (QRES) 파일에서 쿼리 및 데이터를 로드 합니다.| 
||---------*클립보드*---------|
|데이터를 클립보드에 복사|    쿼리 및 데이터 집합을 클립보드로 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 결과 반환| 클립보드에 설정 된 데이터를 내보냅니다. 차트가 표시 되 면 차트를 비트맵으로 내보냅니다.| 
|클립보드로 쿼리| 클립보드로 쿼리를 내보냅니다.|
||---------*검색*---------|
|결과 캐시 지우기| 이전에 실행 한 쿼리의 캐시 된 결과를 지웁니다.| 

### <a name="connections-tab"></a>연결 탭

![Kusto 탐색기 연결 탭](./Images/KustoTools-KustoExplorer/connections-tab.png "연결-탭")

|메뉴|동작|
|----|----------|
||---------*그룹과*---------|
|그룹 추가| 새 Kusto 서버 그룹을 추가 합니다.|
|그룹 이름 바꾸기| 기존 Kusto 서버 그룹의 이름을 바꿉니다.|
|그룹 제거| 기존 Kusto 서버 그룹을 제거 합니다.|
||---------*클러스터*---------|
|가져오기 연결| 연결을 지정 하는 파일에서 연결을 가져옵니다.|
|연결 내보내기| 파일에 대 한 연결 내보내기|
|연결 추가| 새 Kusto 서버 연결을 추가 합니다.| 
|연결 편집| Kusto 서버 연결 속성 편집 대화 상자를 엽니다.|
|연결 제거| Kusto 서버에 대 한 기존 연결을 제거 합니다.|
|새로 고침| Kusto 서버 연결의 속성을 새로 고칩니다.|
||---------*Id 공급자*---------|
|연결 주체 검사| Currents 활성 사용자 정보를 표시 합니다.|
|AAD에서 로그 아웃| AAD에 대 한 연결에서 현재 사용자를 로그 아웃 합니다.|
||---------*데이터 범위*---------|
|캐싱 범위|<ul><li>핫 DataExecute는 [핫 데이터 캐시](../management/cachepolicy.md) 에서만 쿼리를 실행 합니다.</li><li>모든 데이터: 사용 가능한 모든 데이터에 대해 쿼리 실행 (기본값)</li></ul> |
|DateTime 열| 시간 사전 필터에 사용할 수 있는 열의 이름입니다.|
|시간 필터| 시간 사전 필터 값|

### <a name="view-tab"></a>보기 탭

![보기 탭](./Images/KustoTools-KustoExplorer/view-tab.png "보기-탭")

|메뉴|동작|
|----|----------|
||---------*바꿀*---------|
|전체 보기 모드 | 리본 메뉴와 연결 패널을 숨겨서 작업 공간을 최대화 합니다.|
|글꼴 늘리기  | 쿼리 탭의 글꼴 크기와 결과 데이터 표를 늘립니다.|  
|글꼴 줄이기  | 쿼리 탭 및 결과 데이터 표의 글꼴 크기를 줄입니다.|
|레이아웃 다시 설정|도구의 도킹 컨트롤과 창의 레이아웃을 다시 설정 합니다.|
||---------*데이터 뷰*---------|
|보기 다시 설정| 데이터 뷰 설정 다시 설정 (*)|
|열 값 탐색|열 값 분포 표시|
|쿼리 통계에 집중|쿼리가 완료 될 때 쿼리 결과가 아닌 쿼리 통계로 포커스를 변경 합니다.|
|중복 항목 숨기기|쿼리 결과에서 중복 행 제거를 설정/해제 합니다.|
|빈 열 숨기기|쿼리 결과에서 빈 열의 제거를 설정/해제 합니다.|
|단일 열 축소|값이 단수형 인 열 축소/축소|
||---------*데이터 필터링*---------|
|검색에서 행 필터링|쿼리 결과 검색에서 일치 하는 행만 표시 옵션을 설정/해제 합니다 (Ctrl + F).|
||---------*가상화*---------|
|시각화|위의 [시각화](#visualizations-section)를 참조 하세요. |

(*) 데이터 보기 설정: Kusto 탐색기는 고유 열 집합 마다 사용 되는 설정을 추적 합니다. 따라서 열을 다시 정렬 하거나 제거 하면 데이터 뷰가 저장 되 고 동일한 열이 있는 데이터가 검색 될 때마다 다시 사용 됩니다. 뷰를 기본값으로 다시 설정 하려면 **보기** 탭에서 **보기 다시 설정**을 선택 합니다. 

### <a name="tools-tab"></a>도구 탭

![도구 탭](./Images/KustoTools-KustoExplorer/tools-tab.png "도구-탭")

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
|옵션| 응용 프로그램 설정을 구성 하는 도구를 엽니다. [세부 정보](kusto-explorer-options.md)|



### <a name="management-tab"></a>관리 탭

![관리 탭](./Images/KustoTools-KustoExplorer/management-tab.png "관리-탭")

|메뉴             | 동작|
|-----------------|---------|
||---------*권한 있는 보안 주체*---------|
|클러스터 인증 된 보안 주체 관리 |권한 있는 사용자에 대 한 클러스터의 보안 주체를 관리할 수 있습니다.| 
|데이터베이스 인증 된 보안 주체 관리 | 권한 있는 사용자에 대 한 데이터베이스 보안 주체를 관리할 수 있습니다.| 
|테이블 인증 된 보안 주체 관리 | 권한 있는 사용자에 대해 테이블의 보안 주체를 관리할 수 있습니다.| 
|함수 인증 된 보안 주체 관리 | 권한 있는 사용자에 대 한 함수의 보안 주체를 관리할 수 있습니다.| 

### <a name="help-tab"></a>도움말 탭

![도움말 탭](./Images/KustoTools-KustoExplorer/help-tab.png "도움말-탭")

|메뉴             | 동작|
|-----------------|---------|
||---------*설명을*---------|
|도움말             | Kusto 온라인 설명서에 대 한 링크를 엽니다.  | 
|새로운 기능       | 모든 Kusto 탐색기 변경 내용을 나열 하는 문서를 엽니다.|
|보고서 문제      |두 가지 옵션이 있는 대화 상자를 엽니다. <ul><li>서비스와 관련 된 문제 보고</li><li>클라이언트 응용 프로그램의 문제 보고</li></ul> | 
|제안 기능  | Kusto 피드백 포럼에 대 한 링크를 엽니다. | 
|업데이트 확인     | Kusto 탐색기 버전에 대 한 업데이트가 있는지 확인 합니다. | 

## <a name="connections-panel"></a>연결 패널

![대체 텍스트](./Images/KustoTools-KustoExplorer/connectionsPanel.png "연결-패널") 

Kusto 탐색기의 왼쪽 창에는 클라이언트에서 구성 된 모든 클러스터 연결이 표시 됩니다. 각 클러스터에 대해 저장 되는 데이터베이스, 테이블 및 특성 (열)을 표시 합니다. 연결 패널을 사용 하 여 항목을 선택 (주 패널의 검색/쿼리에 대 한 암시적 컨텍스트를 설정) 하거나 항목을 두 번 클릭 하 여 검색/쿼리 패널에 이름을 복사할 수 있습니다.

실제 스키마가 많은 경우 (예: 수백 개의 테이블이 있는 데이터베이스) CTRL + F를 눌러 스키마를 검색 하 고 찾으려는 엔터티 이름에 대/소문자를 구분 하지 않는 부분 문자열을 입력할 수 있습니다.

Kusto 탐색기는 쿼리 창에서 연결 패널을 제어 하도록 지원 합니다.
스크립트에 매우 유용 합니다. 예를 들어 명령을 사용 하 여 스크립트 파일을 시작 하면 다음 구문을 사용 하 여 스크립트에서 데이터를 쿼리 하는 클러스터/데이터베이스에 연결 하는 데 사용할 수 있습니다. 일반적으로 또는 유사 하 게를 사용 하 여 각 줄을 실행 해야 합니다 `F5` .

```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

### <a name="controlling-the-user-identity-used-for-connecting-to-kusto"></a>Kusto에 연결 하는 데 사용 되는 사용자 id 제어

새 연결을 추가할 때 사용 되는 기본 보안 모델은 AAD 페더레이션 보안 이며, 기본 AAD 사용자 환경을 사용 하 여 Azure Active Directory를 통해 인증이 수행 됩니다.

경우에 따라 AAD에서 사용할 수 있는 것 보다 더 세밀 하 게 인증 매개 변수를 제어 해야 할 수도 있습니다. 그렇다면 "고급: 연결 문자열" 편집 상자를 확장 하 고 유효한 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 값을 제공할 수 있습니다.

예를 들어, 여러 AAD 테 넌 트에 있는 사용자는 때때로 특정 AAD 테 넌 트에 id의 특정 "프로젝션"을 사용 해야 합니다. 아래와 같은 연결 문자열을 제공 하 여이 작업을 수행할 수 있습니다 (대문자로 단어를 특정 값으로 바꾸기).

```
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

고유한 것은입니다 `AAD_TENANT_OF_CLUSTER` . 즉, 클러스터가 호스트 되는 aad 테 넌 트의 도메인 이름 또는 aad 테 넌 트 ID (GUID)입니다. 즉, 클러스터를 소유 하는 조직 도메인 이름 (예: `contoso.com` USER_DOMAIN)은이 테 넌 트에 초대 된 사용자의 id (예: `joe@fabrikam.com` )입니다. 

>[!Note]
> 사용자의 도메인 이름은 클러스터를 호스트 하는 테 넌 트의 도메인 이름과 같을 필요는 없습니다.

## <a name="table-row-colors"></a>테이블 행 색

Kusto 탐색기는 결과 창에서 각 행의 심각도 또는 세부 정보 표시 수준을 "추측" 하 여 적절 하 게 색을 표시 하려고 합니다. 이렇게 하려면 각 열의 고유 값을 알려진 패턴 집합 ("경고", "오류" 등)과 일치 합니다.

출력 색 구성표를 수정 하거나이 동작을 해제 하려면 **도구** 메뉴에서 **옵션**  >  **결과 뷰어**  >  **자세한 정도 색 구성표**를 선택 합니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/ke-color-scheme.png)

## <a name="search-mode"></a>검색 + + 모드

1. 홈 탭의 쿼리 드롭다운에서 "검색 + +"를 선택 합니다.
2. **여러 테이블** 을 선택 하 고 **테이블 선택**에서 검색할 테이블을 정의 합니다.
3. 편집 상자에 검색 구를 입력 하 고 **이동** 을 선택 합니다.
4. 테이블/시간 슬롯 표의 열 지도에 표시 되는 용어와 표시 되는 위치를 보여 줍니다.
5. 표에서 셀을 선택 하 고 **자세히 보기** 를 선택 하 여 관련 항목을 표시 합니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/ke-search-beta.jpg "ke-검색-베타") 

## <a name="query-mode"></a>쿼리 모드

Kusto. 탐색기에는 임시 쿼리를 작성, 편집 및 실행할 수 있는 강력한 스크립트 모드가 있습니다. 스크립트 모드는 구문 강조 표시 및 IntelliSense와 함께 제공 되므로 Kusto CSL 언어로 빠르게 이동할 수 있습니다.

### <a name="basic-queries"></a>기본 쿼리

테이블 로그가 있는 경우 다음을 입력 하 여 탐색을 시작할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | count 
```

커서를이 줄에 배치 하면 회색으로 표시 됩니다. ' F5 ' 키를 누르면 쿼리가 실행 됩니다. 

다음은 몇 가지 추가 예제 쿼리입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

## <a name="importing-a-local-file-into-a-kusto-table"></a>Kusto 테이블로 로컬 파일 가져오기

Kusto 탐색기는 컴퓨터의 파일을 Kusto 테이블에 업로드 하는 편리한 방법을 제공 합니다.

1. 파일과 일치 하는 스키마를 사용 하 여 테이블을 만들었는지 확인 합니다. 예를 들어 [create table 명령을 사용 합니다.](../management/tables.md)

1. 파일 확장명이 파일의 내용에 적합 한지 확인 합니다. 다음은 그 예입니다.
    * 파일에 쉼표로 구분 된 값이 포함 된 경우 파일의 확장명이 .csv 인지 확인 합니다.
    * 파일에 탭으로 구분 된 값이 포함 된 경우 파일의 확장명이 tsv 인지 확인 합니다.

1. [연결 패널](#connections-panel)에서 대상 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새로 고침**을 선택 하 여 테이블을 표시 합니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/right-click-refresh-schema.png "마우스 오른쪽 단추 클릭-새로 고침-스키마")

1. [연결 패널](#connections-panel)에서 대상 테이블을 마우스 오른쪽 단추로 클릭 하 고 **로컬 파일에서 데이터 가져오기**를 선택 합니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/right-click-import-local-file.png "마우스 오른쪽 단추로 클릭-로컬 파일")

1. 업로드할 파일을 선택 하 고 **열기**를 선택 합니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/import-local-file-choose-files.png "가져오기-로컬 파일-파일 선택")

    진행률 표시줄에 진행률이 표시 되 고 작업이 완료 되 면 대화 상자가 표시 됩니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/import-local-file-progress.png "가져오기-로컬-파일-진행률")

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/import-local-file-complete.png "가져오기-로컬-파일-전체")

1. 테이블의 데이터를 쿼리 합니다 ( [연결 패널](#connections-panel)에서 테이블을 두 번 클릭).

## <a name="managing-authorized-principals"></a>권한 있는 보안 주체 관리

Kusto. 탐색기는 클러스터, 데이터베이스, 테이블 또는 함수 인증 된 보안 주체를 관리 하는 편리한 방법을 제공 합니다.

> [!Note]
> [관리자](../management/access-control/role-based-authorization.md) 만 인증 된 보안 주체를 자체 범위에 추가 하거나 삭제할 수 있습니다.

1. [연결 패널](#connections-panel)에서 대상 엔터티를 마우스 오른쪽 단추로 클릭 하 고 **권한이 부여 된 보안 주체 관리**를 선택 합니다. (관리 메뉴에서이 작업을 수행할 수도 있습니다.)

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/right-click-manage-authorized-principals.png "마우스 오른쪽 단추 클릭-권한 부여-보안 주체")

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/manage-authorized-principals-window.png "관리 권한-보안 주체-창")

1. 새 인증 된 보안 주체를 추가 하려면 **보안 주체 추가**를 선택 하 고, 보안 주체 세부 정보를 제공 하 고, 작업을 확인 합니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/add-authorized-principals-window.png "추가 권한 부여-보안 주체-창")

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/confirm-add-authorized-principals.png "확인-추가 권한 부여-보안 주체")

1. 권한 있는 기존 보안 주체를 삭제 하려면 **보안 주체 삭제** 를 선택 하 고 작업을 확인 합니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/confirm-drop-authorized-principals.png "확인-삭제 권한-보안 주체")

## <a name="sharing-queries-and-results-by-email"></a>전자 메일로 쿼리 및 결과 공유

Kusto 탐색기는 쿼리 및 쿼리 결과를 전자 메일로 공유 하는 편리한 방법을 제공 합니다. **클립보드로 내보내기**를 선택 하 고, Kusto. 탐색기에서 다음 항목을 클립보드에 복사 합니다.
1. 쿼리
1. 쿼리 결과 (테이블 또는 차트)
1. Kusto 클러스터 및 데이터베이스에 대 한 연결 세부 정보
1. 쿼리를 자동으로 다시 실행 하는 링크

작동 방식은 다음과 같습니다.

1. Kusto 탐색기에서 쿼리를 실행 합니다.
1. **클립보드로 내보내기** 또는 누르기를 선택 합니다. `Ctrl+Shift+C`

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/menu-export.png "메뉴-내보내기")

1. 예를 들어 새 Outlook 메시지를 엽니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/share-results.png "공유-결과")
    
1. 클립보드의 내용을 Outlook 메시지에 붙여넣습니다.

    ![대체 텍스트](./Images/KustoTools-KustoExplorer/share-results-2.png "공유-결과-2")

## <a name="client-side-query-parametrization"></a>클라이언트 쪽 쿼리 parametrization

> [!WARNING]
> Kusto에는 두 가지 유형의 query parametrization 기술이 있습니다.
> * [언어 통합 쿼리 parametrization](../query/queryparametersstatement.md) 는 쿼리 엔진의 일부로 구현 되며 서비스를 프로그래밍 방식으로 쿼리 하는 응용 프로그램에서 사용 됩니다.
>
> * 아래에 설명 된 클라이언트 쪽 쿼리 parametrization는 Kusto 탐색기 응용 프로그램의 기능입니다. 서비스에서 실행 되도록 보내기 전에 쿼리에 대해 문자열 바꾸기 작업을 사용 하는 것과 같습니다. 아래에 설명 된 구문은 쿼리 언어 자체에 포함 되지 않으며, Kusto 탐색기 이외의 방법으로 서비스에 쿼리를 보낼 때 사용할 수 없습니다.

여러 쿼리나 여러 탭에서 동일한 값을 사용 하려는 경우에는 변경 하기가 어렵습니다. 그러나 Kusto 탐색기는 쿼리 매개 변수를 지원 합니다. 매개 변수는 대괄호로 표시 됩니다 {} . 예: `{parameter1}`

스크립트 편집기는 쿼리 매개 변수를 강조 표시 합니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/parametrized-query-1.png "매개 변수가 있는-1")

기존 쿼리 매개 변수를 쉽게 정의 하 고 편집할 수 있습니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/parametrized-query-2.png "매개 변수가 있는-2")

![대체 텍스트](./Images/KustoTools-KustoExplorer/parametrized-query-3.png "매개 변수가 있는-3")

스크립트 편집기에는 이미 정의 된 쿼리 매개 변수에 대 한 IntelliSense도 있습니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/parametrized-query-4.png "매개 변수가 있는-4")

매개 변수 **집합** 콤보 상자에 나열 된 여러 매개 변수 집합이 있을 수 있습니다.
**새로 추가** 및 **삭제 현재** 를 사용 하 여 매개 변수 집합 목록을 조작 합니다.

![대체 텍스트](./Images/KustoTools-KustoExplorer/parametrized-query-5.png "매개 변수가 있는-5")

쿼리 매개 변수는 탭 간에 공유 되므로 쉽게 다시 사용할 수 있습니다.

## <a name="deep-linking-queries"></a>심층 연결 쿼리

### <a name="overview"></a>개요
브라우저에서 열 때 Kusto. 탐색기는 로컬에서 시작 하 여 지정 된 Kusto 데이터베이스에서 특정 쿼리를 실행 하는 URI를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항
쿼리는 Internet Explorer 제한으로 인해 ~ 2000 자로 제한 됩니다 (클러스터 및 데이터베이스 이름 길이에 따라 달라 지므로 제한이 근사치 임) https://support.microsoft.com/kb/208427 . 문자 제한에 도달할 가능성을 줄이기 위해 아래의 [짧은 링크 가져오기](#getting-shorter-links)를 참조 하세요.

URI 형식은 https:// <ClusterCname> 입니다. kusto.windows.net/ <DatabaseName> ? query =<QueryToExecute>

다음은 그 예입니다.  https://help.kusto.windows.net/Samples?query=StormEvents+%7c+limit+10
 
이 URI는 Kusto 탐색기를 열고, `help` kusto 클러스터에 연결 하 고, 데이터베이스에서 지정 된 쿼리를 실행 합니다. `Samples` Kusto. 탐색기 인스턴스가 이미 실행 중인 경우 실행 중인 인스턴스는 새 탭을 열고 쿼리를 실행 합니다.

**보안 정보**: 보안상의 이유로, 제어 명령에 대해 딥 링크를 사용할 수 없습니다.



### <a name="creating-a-deep-link"></a>딥 링크 만들기
딥 링크를 만드는 가장 쉬운 방법은 Kusto. 탐색기에서 쿼리를 작성 한 다음 클립보드로 내보내기를 사용 하 여 쿼리 (딥 링크 및 결과 포함)를 클립보드에 복사 하는 것입니다. 그런 다음 전자 메일을 통해 공유할 수 있습니다.
        
전자 메일에 복사 되는 경우 딥 링크는 작은 글꼴로 표시 됩니다. 예를 들어:

https://help.kusto.windows.net:443/Samples[[쿼리를 실행 하려면 클릭](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

첫 번째 링크는 Kusto. 탐색기를 열고 클러스터 및 데이터베이스 컨텍스트를 적절 하 게 설정 합니다.
두 번째 링크 (쿼리를 실행 하려면 클릭)가 딥 링크입니다. 전자 메일 메시지에 대 한 링크로 이동 하 고 CTRL + K를 누르면 실제 URL을 볼 수 있습니다.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>딥 링크 및 매개 변수가 있는 쿼리

딥 링크와 함께 매개 변수가 있는 쿼리를 사용할 수 있습니다.

1. 매개 변수가 있는 쿼리 (예:)로 형성 될 쿼리를 만듭니다. `KustoLogs | where Timestamp > ago({Period}) | count` 
2. 이 경우 URI의 모든 쿼리 매개 변수에 대 한 매개 변수를 제공 합니다.

`https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

### <a name="getting-shorter-links"></a>짧은 링크 가져오기

쿼리가 길어질 수 있습니다. 쿼리가 최대 길이를 초과 하는 가능성을 줄이려면 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto 클라이언트 라이브러리에서 사용할 수 있는 메서드를 사용 합니다. 이 메서드는 더 간결한 버전의 쿼리를 생성 합니다. 더 짧은 형식은 Kusto 탐색기에서도 인식 됩니다.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

다음 변환을 적용 하 여 쿼리를 더 간결 하 게 만들 수 있습니다.

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto 탐색기 명령줄 인수

Kusto 탐색기는 다음 구문에서 몇 가지 명령줄 인수를 지원 합니다 (순서는 중요).

[*LocalScriptFile*] [*QueryString*]

여기서
* *LocalScriptFile* 는 로컬 컴퓨터에서 확장명이 있어야 하는 스크립트 파일의 이름입니다 `.kql` . 이러한 파일이 있는 경우 파일을 시작 하면이 파일이 자동으로 로드 됩니다.
* *QueryString* 은 HTTP 쿼리 문자열 형식을 사용 하 여 서식이 지정 된 문자열입니다. 이 메서드는 아래 표에 설명 된 대로 추가 속성을 제공 합니다.

예를 들어 `c:\temp\script.kql` 클러스터, 데이터베이스와 통신 하도록 구성 된 스크립트 파일과 함께 Kusto 탐색기를 시작 하려면 `help` `Samples` 다음 명령을 사용 합니다.

```
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|인수  |설명                                                               |
|----------|--------------------------------------------------------------------------|
|**실행할 쿼리**                                                                 |
|`query`   |실행할 쿼리 (base64 인코딩)입니다. 비어 있는 경우를 사용 `querysrc` 합니다.          |
|`querysrc`|실행할 쿼리를 보유 하는 파일 또는 blob의 URL ( `query` 가 비어 있는 경우)입니다.|
|**Kusto 클러스터에 연결**                                                  |
|`uri`     |연결할 Kusto 클러스터의 연결 문자열입니다.                 |
|`name`    |Kusto 클러스터에 대 한 연결의 표시 이름입니다.                  |
|**연결 그룹**                                                                 |
|`path`    |다운로드할 연결 그룹 파일의 URL (URL 인코딩)입니다.             |
|`group`   |연결 그룹의 이름입니다.                                         |
|`filename`|연결 그룹을 보유 하는 로컬 파일입니다.                              |

## <a name="kustoexplorer-connection-files"></a>Kusto 탐색기 연결 파일

Kusto 탐색기는 폴더의 연결 설정을 유지 합니다. `%LOCALAPPDATA%\Kusto.Explorer`
연결 그룹의 목록은 내에 유지 되 `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` 고 각 연결 그룹은의 전용 파일 내에 유지 됩니다 `%LOCALAPPDATA%\Kusto.Explorer\Connections\` .

### <a name="format-of-connection-group-files"></a>연결 그룹 파일의 형식

파일 `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` 위치가 인 경우  

다음 속성을 사용 하 여 개체 배열의 XML 직렬화입니다 `ServerGroupDescription` .

```
  <ServerGroupDescription>
    <Name>`Connection Group name`</Name>
    <Details>`Full path to XML file containing the list of connections`</Details>
  </ServerGroupDescription>
```

예제:

```
<?xml version="1.0"?>
<ArrayOfServerGroupDescription xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerGroupDescription>
    <Name>Connections</Name>
    <Details>C:\Users\alexans\AppData\Local\Kusto.Explorer\UserConnections.xml</Details>
  </ServerGroupDescription>
</ArrayOfServerGroupDescription>  
```

### <a name="format-of-connection-list-files"></a>연결 목록 파일의 형식

파일 위치는 `%LOCALAPPDATA%\Kusto.Explorer\Connections\` 입니다.

다음 속성을 사용 하 여 개체 배열의 XML 직렬화입니다 `ServerDescriptionBase` .

```
   <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>`Connection name`</Name>
    <Details>`Details as shown in UX, usually full URI`</Details>
    <ConnectionString>`Full connection string to the cluster`</ConnectionString>
  </ServerDescriptionBase>
```

예제:

```
<?xml version="1.0"?>
<ArrayOfServerDescriptionBase xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>Help</Name>
    <Details>https://help.kusto.windows.net</Details>
    <ConnectionString>Data Source=https://help.kusto.windows.net:443;Federated Security=True</ConnectionString>
  </ServerDescriptionBase>
</ArrayOfServerDescriptionBase>
```

## <a name="resetting-kustoexplorer"></a>Kusto 탐색기 다시 설정

필요한 경우 Kusto. 탐색기를 완전히 다시 설정할 수 있습니다. 완전히 제거 되 고 처음부터 설치 해야 하는 경우에만 컴퓨터에 배포 된 Explorer를 점진적으로 다시 설정 하려면 다음 절차를 따르십시오.

1. Windows에서 프로그램 **변경 또는 제거** ( **프로그램 및 기능이**라고도 함)를 엽니다.
1. 이름이로 시작 하는 모든 항목을 선택 `Kusto.Explorer` 합니다.
1. **제거**를 선택합니다.

   응용 프로그램을 제거 하지 못할 경우 (예를 들어 ClickOnce 응용 프로그램을 사용 하는 경우 알려진 문제)이 작업을 수행 하는 방법을 설명 하는 [이 스택 오버플로 문서](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer) 를 참조 하세요.

1. 폴더를 삭제 `%LOCALAPPDATA%\Kusto.Explorer` 합니다. 그러면 모든 연결, 기록 등이 제거 됩니다.

1. 폴더를 삭제 `%APPDATA%\Kusto` 합니다. 그러면 Kusto. 탐색기 토큰 캐시가 제거 됩니다. 모든 클러스터에 다시 인증 해야 합니다.

또한 특정 버전의 Kusto. 탐색기로 되돌릴 수 있습니다.

1. `appwiz.cpl`를 실행합니다.
1. **Kusto 탐색기** 를 선택 하 고 **제거/변경**을 선택 합니다.
3. **응용 프로그램을 이전 상태로 복원을**선택 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="kustoexplorer-fails-to-start"></a>Kusto 탐색기 시작 실패

#### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>시작 하는 동안 또는 이후에 탐색기에서 오류 대화 상자 표시

**증상:**

시작 시 Kusto 탐색기에서 오류를 표시 합니다. `InvalidOperationException`

**가능한 해결 방법:**

이 오류가 발생 하면 운영 체제가 손상 되거나 일부 필수 모듈이 누락 될 수 있습니다.
누락 되거나 손상 된 시스템 파일을 확인 하려면 여기에 설명 된 단계를 따르세요.   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

### <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>업데이트 없는 경우에도 항상 탐색기에서 다운로드 합니다.

**증상:**

Kusto. 탐색기를 열 때마다 새 버전를 설치할지 묻는 메시지가 표시 됩니다. Kusto 탐색기는 이미 설치 된 버전을 실제로 업데이트 하지 않고 전체 패키지를 다운로드 합니다.

**가능한 해결 방법:**

이는 로컬 ClickOnce 저장소의 손상으로 인해 발생할 수 있습니다. 관리자 권한 명령 프롬프트에서 다음 명령을 실행 하 여 로컬 ClickOnce 저장소를 지울 수 있습니다.
> [!Important]
> 1. ClickOnce 응용 프로그램의 다른 인스턴스 또는이 있는 경우 `dfsvc.exe` 이 명령을 실행 하기 전에 해당 인스턴스를 종료 합니다.
> 2. 앱 바로 가기에 저장 된 원래 설치 위치에 대 한 액세스 권한이 있는 경우 ClickOnce 앱은 다음에 실행할 때 자동으로 다시 설치 됩니다. 앱 바로 가기는 삭제 되지 않습니다.

```
rd /q /s %userprofile%\appdata\local\apps\2.0
```

[설치 미러](#getting-the-tool)중 하나에서 Kusto. 탐색기를 다시 설치 해 보세요.

#### <a name="clickonce-error-cannot-start-application"></a>ClickOnce 오류: 응용 프로그램을 시작할 수 없습니다.

**열릴**  

* 프로그램이 시작 되지 않으며 다음을 포함 하는 오류를 표시 합니다.`External component has thrown an exception`
* 프로그램이 시작 되지 않으며 다음을 포함 하는 오류를 표시 합니다.`Value does not fall within the expected range`
* 프로그램이 시작 되지 않으며 다음을 포함 하는 오류를 표시 합니다.`The application binding data format is invalid.` 
* 프로그램이 시작 되지 않으며 다음을 포함 하는 오류를 표시 합니다.`Exception from HRESULT: 0x800736B2`

다음 오류 대화 상자를 클릭 하 여 오류 정보를 탐색할 수 있습니다 `Details` .

![대체 텍스트](./Images/KustoTools-KustoExplorer/clickonce-err-1.jpg "clickonce-오류-1")

```
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**제안 된 솔루션 단계:**

1. `Programs and Features`()를 사용 하 여 Kusto. 탐색기 응용 프로그램을 제거 합니다. `appwiz.cpl`

1. 실행 `CleanOnlineAppCache` 을 시도한 후 Kusto 탐색기를 다시 설치 해 보세요. 관리자 권한 명령 프롬프트에서 다음을 수행 합니다. 
    
    ```
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    [설치 미러](#getting-the-tool)중 하나에서 Kusto 탐색기를 다시 설치 합니다.

1. 그래도 오류가 계속 발생 하면 로컬 ClickOnce 저장소를 삭제 합니다. 앱 바로 가기에 저장 된 원래 설치 위치에 대 한 액세스 권한이 있는 경우 ClickOnce 앱은 다음에 실행할 때 자동으로 다시 설치 됩니다. 앱 바로 가기는 삭제 되지 않습니다.

관리자 권한 명령 프롬프트에서 다음을 수행 합니다.

    ```
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Install Kusto.Explorer again from one of the [installation mirrors](#getting-the-tool)

1. 그래도 오류가 계속 발생 하면 임시 배포 파일을 제거 하 고 Kusto. Explorer local AppData 폴더의 이름을 변경 합니다.

    관리자 권한 명령 프롬프트에서 다음을 수행 합니다.

    ```
    rd /s/q %userprofile%\AppData\Local\Temp\Deployment
    ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
    ```

    [설치 미러](#getting-the-tool) 중 하나에서 다시 Kusto 탐색기를 설치 합니다.

1. 관리자 권한 명령 프롬프트에서 Kusto. Explorer.exe에서 연결을 복원 하려면 다음을 수행 합니다.

    ```
    copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
    ```

1. 그래도 오류가 발생 하면 아래에서 LogVerbosityLevel 문자열 값 1을 만들어 자세한 ClickOnce 로깅을 사용 하도록 설정 합니다.

`HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment`다시 재현 하 고 자세한 정보 출력을로 보냅니다 KEBugReport@microsoft.com . 

#### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce 오류: 컴퓨터에 보안 위험이 발생할 수 있으므로 관리자가이 응용 프로그램을 차단 했습니다.

**증상:**  
다음 오류 중 하나를 사용 하 여 프로그램을 설치 하지 못했습니다.
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**해결 방법:**

1. 이는 기본 ClickOnce 신뢰 프롬프트 동작을 재정의 하는 다른 응용 프로그램 때문에 발생할 수 있습니다. [이 방법 문서에](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)설명 된 대로 기본 구성 설정을 확인 하 고, 컴퓨터의 실제 항목과 비교 하 고, 필요에 따라 다시 설정할 수 있습니다.

#### <a name="cleanup-application-data"></a>응용 프로그램 데이터 정리

경우에 따라 이전 문제 해결 단계를 시작 하는 데 도움이 되지 않는 경우 로컬에 저장 된 데이터를 정리 하는 것이 도움이 될 수 있습니다.

Kusto. 탐색기 응용 프로그램에서 저장 한 데이터는에서 찾을 수 `C:\Users\\[your alias]\AppData\Local\Kusto.Explorer` 있습니다.

> [!NOTE]
> 데이터를 정리 하면 열린 탭 (복구 폴더), 저장 된 연결 (연결 폴더) 및 응용 프로그램 설정 (UserSettings 폴더)이 손실 됩니다.