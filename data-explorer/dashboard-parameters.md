---
title: Azure 데이터 탐색기 대시보드의 매개 변수
description: 대시보드 필터의 문서 블록으로 매개 변수를 사용 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: reference
ms.date: 06/09/2020
ms.openlocfilehash: 6a06e5ea90bb16466c3550c8de07f930477dad96
ms.sourcegitcommit: b0cbb89e88b64a36880e6d34b4d6779283174456
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637374"
---
# <a name="use-parameters-in-azure-data-explorer-dashboards"></a>Azure 데이터 탐색기 대시보드에서 매개 변수 사용

매개 변수는 Azure 데이터 탐색기 대시보드의 대시보드 필터에 대 한 구성 요소로 사용 됩니다. 이러한 데이터는 대시보드 범위에서 관리 되며, 쿼리에 추가 하 여 기본 시각적 개체에서 제공 하는 데이터를 필터링 할 수 있습니다. 쿼리에서는 하나 이상의 매개 변수를 사용할 수 있습니다. 이 문서에서는 Azure 데이터 탐색기 대시보드에서 매개 변수와 연결 된 필터를 만들고 사용 하는 방법을 설명 합니다. 

> [!NOTE]
> 매개 변수 관리는 편집 모드에서 대시보드 편집기로 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[Azure 데이터 탐색기 대시보드를 사용 하 여 데이터 시각화](azure-data-explorer-dashboards.md)

## <a name="view-parameters-list"></a>매개 변수 목록 보기

대시보드 맨 위에 있는 **매개 변수** 단추를 선택 하 여 모든 대시보드 매개 변수 목록을 볼 수 있습니다.

:::image type="content" source="media/dashboard-parameters/dashboard-icons.png" alt-text="대시보드 위쪽의 매개 변수 단추":::

## <a name="create-a-parameter"></a>매개 변수 만들기

매개 변수를 만들려면 오른쪽 창의 위쪽에 있는 **새 매개 변수** 단추를 선택 합니다.

:::image type="content" source="media/dashboard-parameters/new-parameter-button.png" alt-text="새 매개 변수 단추":::

### <a name="properties"></a>속성

1. **매개 변수 추가** 창에서 아래 자세히 설명 된 속성을 구성 합니다.

:::image type="content" source="media/dashboard-parameters/properties.png" alt-text="매개 변수 속성 추가":::

|필드  |Description |
|---------|---------|
|**매개 변수 표시 이름**    |   대시보드 또는 편집 카드에 표시 되는 매개 변수의 이름입니다.      |
|**매개 변수 유형**    |다음 중 하나<ul><li>**단일 선택**: 필터에서 매개 변수에 대 한 입력으로 하나의 값만 선택할 수 있습니다.</li><li>**여러 선택**: 매개 변수에 대 한 입력으로 필터에서 하나 이상의 값을 선택할 수 있습니다.</li><li>**시간 범위**: 시간을 기준으로 쿼리 및 대시보드를 필터링 하는 추가 매개 변수를 만들 수 있습니다. 모든 대시보드는 기본적으로 시간 범위를 선택 합니다.</li></ul>    |
|**변수 이름**     |   쿼리에서 사용 되는 매개 변수의 이름입니다.      |
|**데이터 형식**    |    매개 변수 값의 데이터 형식입니다.     |
|**대시보드 필터로 고정**   |   매개 변수 기반 필터를 대시보드에 고정 하거나 대시보드에서 고정 해제 합니다.       |
|**원본**     |    매개 변수 값의 소스입니다. <ul><li>**고정 값**: 정적 필터 값을 수동으로 도입 했습니다. </li><li>**쿼리**: KQL 쿼리를 사용 하 여 동적으로 값을 도입 했습니다.  </li></ul>    |
|**"모두 선택" 값 추가**    |   단일 선택 및 다중 선택 매개 변수 형식에만 적용할 수 있습니다. 모든 매개 변수 값에 대 한 데이터를 검색 하는 데 사용 됩니다. 이 값을 쿼리에 제공 하 여 기능을 제공 해야 합니다. 이러한 쿼리를 작성 하는 방법에 대 한 자세한 예제 [는 다중 선택 쿼리 기반 매개 변수 사용](#use-the-multiple-selection-query-based-parameter) 을 참조 하세요.     |

## <a name="manage-parameters-in-parameter-card"></a>매개 변수 카드에서 매개 변수 관리

매개 변수 카드의 세 점 메뉴에서 **편집**, **매개 변수 복제**, **삭제**또는 대시보드 Pin **에서 대시보드에 고정 해제**를 선택 / **Pin to dashboard**합니다.

매개 변수 카드에서 다음 표시기를 볼 수 있습니다.
* 매개 변수 표시 이름 
* 변수 이름 
* 매개 변수가 사용 된 쿼리 수
* 대시보드의 고정/고정 해제 상태

:::image type="content" source="media/dashboard-parameters/modify-parameter.png" alt-text="매개 변수 수정":::

## <a name="use-parameters-in-your-query"></a>쿼리에서 매개 변수 사용

쿼리에 사용 되는 필터를 만들려면 쿼리에 매개 변수를 사용 해야 합니다. 정의 되 면 **쿼리** 페이지 > 필터 위쪽 막대와 쿼리 intellisense에서 매개 변수를 볼 수 있습니다.

:::image type="content" source="media/dashboard-parameters/query-intellisense.png" alt-text="위쪽 막대 및 intellisense의 매개 변수를 참조 하세요.":::

> [!NOTE]
> 쿼리에서 매개 변수를 사용 하지 않는 경우 필터는 비활성화 된 상태로 유지 됩니다. 쿼리에 매개 변수가 추가 되 면 필터가 활성화 됩니다.

## <a name="use-different-parameter-types"></a>다른 매개 변수 유형 사용

여러 가지 대시보드 매개 변수 형식이 지원 됩니다. 다음 예에서는 다양 한 매개 변수 형식에 대해 쿼리에서 매개 변수를 사용 하는 방법을 설명 합니다. 

### <a name="use-the-default-time-range-parameter"></a>기본 시간 범위 매개 변수 사용

모든 대시보드의 *시간 범위* 매개 변수는 기본적으로입니다. 쿼리에서 사용 되는 경우에만 대시보드에 필터로 표시 됩니다. 매개 변수 키워드 및를 사용 `_startTime` `__endTime` 하 여 다음 예제에 표시 된 대로 쿼리에서 기본 시간 범위 매개 변수를 사용 합니다.

```kusto
EventsAll
| where Repo.name has 'Microsoft'
| where CreatedAt between (_startTime.._endTime)
| summarize TotalEvents = count() by RepoName=tostring(Repo.name)
| top 5 by TotalEvents
```

저장 되 면 시간 범위 필터가 대시보드에 표시 됩니다. 이제 카드의 데이터를 필터링 하는 데 사용할 수 있습니다. 드롭다운: **시간 범위** (마지막 x 분/시간/일) 또는 **사용자 지정 시간 범위**에서 선택 하 여 대시보드를 필터링 할 수 있습니다.

:::image type="content" source="media/dashboard-parameters/time-range.png" alt-text="사용자 지정 시간 범위를 사용 하 여 필터링":::

### <a name="use-the-single-selection-fixed-value-parameter"></a>단일 선택 고정 값 매개 변수 사용

고정 값 매개 변수는 사용자가 지정 하는 미리 정의 된 값을 기반으로 합니다. 다음 예제에서는 단일 선택 고정 값 매개 변수를 만드는 방법을 보여 줍니다.

#### <a name="create-the-parameter"></a>매개 변수 만들기

1. 매개 **변수를 선택 하** 여 **매개** 변수 창을 열고 **새 매개 변수**를 선택 합니다.

1. 세부 정보를 다음과 같이 입력 합니다.

    * **매개 변수 표시 이름**: 회사
    * **매개 변수 유형**: 단일 선택
    * **변수 이름**:`_company`
    * **데이터 형식**: 문자열
    * **대시보드 필터로 고정**: 선택 됨
    * **원본**: 고정 값
    
    이 예에서는 다음 값을 사용 합니다.
    
    | 값      | 매개 변수 표시 이름 |
    |------------|------------------------|
    | 로그    | Google                 |
    | microsoft | Microsoft              |
    | facebook  | Facebook               |
    | aws       | AWS                    |
    | uber      | Uber                   |
    
    * **모두 선택** 값 추가: 선택 취소
    * 기본값: Microsoft

1. **완료** 를 선택 하 여 매개 변수를 만듭니다.

매개 변수는 **매개 변수** 측면 창에서 볼 수 있지만 현재 시각적 개체에는 사용 되지 않습니다.

:::image type="content" source="media/dashboard-parameters/start-end-side-pane.png" alt-text="왼쪽 창에서 시간 종료 시간 매개 변수 시작":::

#### <a name="use-the-parameter"></a>매개 변수 사용

1. 변수 이름을 사용 하 여 새 *회사* 매개 변수를 사용 하 여 예제 쿼리를 실행 합니다 `_company` .

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

    새 매개 변수가 대시보드 맨 위에 있는 매개 변수 목록에 표시 됩니다.

1. 다른 값을 선택 하 여 시각적 개체를 업데이트 합니다.

    :::image type="content" source="media/dashboard-parameters/top-five-repos.png" alt-text="상위 5 개 리포지토리 결과":::

### <a name="use-the-multiple-selection-fixed-value-parameters"></a>다중 선택 고정 값 매개 변수 사용

고정 값 매개 변수는 사용자가 지정 하는 미리 정의 된 값을 기반으로 합니다. 다음 예제에서는 다중 선택 고정 값 매개 변수를 만들고 사용 하는 방법을 보여 줍니다.

#### <a name="create-the-parameters"></a>매개 변수 만들기

1. 매개 **변수를 선택 하** 여 **매개** 변수 창을 열고 **새 매개 변수**를 선택 합니다.

1. 다음 변경 내용과 함께 [단일 선택 고정 값 매개 변수 사용](#use-the-single-selection-fixed-value-parameter) 에 설명 된 대로 세부 정보를 입력 합니다.

    * **매개 변수 표시 이름**: 회사
    * **매개 변수 유형**: 다중 선택
    * **변수 이름**:`_companies`

1. **완료** 를 클릭 하 여 매개 변수를 만듭니다.

새 매개 변수는 **매개 변수** 측면 창에서 볼 수 있지만 현재 시각적 개체에는 사용 되지 않습니다.

:::image type="content" source="media/dashboard-parameters/companies-side-pane.png" alt-text="회사 측 창":::

#### <a name="use-the-parameters"></a>매개 변수 사용 

1. 변수를 사용 하 여 새 *회사* 매개 변수를 사용 하 여 샘플 쿼리를 실행 합니다 `_companies` .

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | extend RepoName = tostring(Repo.name)
    | where Type == "WatchEvent"
    | where RepoName has_any (_companies)
    | summarize WatchEvents=count() by RepoName
    | top 5 by WatchEvents
    ```

    새 매개 변수가 대시보드 맨 위에 있는 매개 변수 목록에 표시 됩니다.  

1. 시각적 개체를 업데이트 하려면 하나 이상의 다른 값을 선택 합니다.

    :::image type="content" source="media/dashboard-parameters/select-companies.png" alt-text="회사 선택":::

### <a name="use-the-single-selection-query-based-parameter"></a>단일 선택 쿼리 기반 매개 변수 사용

쿼리 기반 매개 변수 값은 매개 변수 쿼리를 실행 하 여 대시보드를 로드 하는 동안 검색 됩니다. 다음 예에서는 단일 선택 쿼리 기반 매개 변수를 만들고 사용 하는 방법을 보여 줍니다.

#### <a name="create-the-parameter"></a>매개 변수 만들기

1. 매개 **변수를 선택 하** 여 **매개** 변수 창을 열고 **새 매개 변수**를 선택 합니다.

2. 다음 변경 내용과 함께 [단일 선택 고정 값 매개 변수 사용](#use-the-single-selection-fixed-value-parameter) 에 설명 된 대로 세부 정보를 입력 합니다.

    * **매개 변수 표시 이름**: 이벤트
    * **변수 이름**:`_event`
    * **원본**: 쿼리
    * **데이터 원본**: GitHub
    * **쿼리 추가** 를 선택 하 고 다음 쿼리를 입력 합니다. **완료**를 선택합니다.

    ```kusto
    EventsAll
    | distinct Type
    | order by Type asc
    ```

    * **값**: 형식
    * **표시 이름**: 형식
    * **기본값**: WatchEvent

1. **완료** 를 선택 하 여 매개 변수를 만듭니다.

#### <a name="use-a-parameter-in-the-query"></a>쿼리에서 매개 변수 사용

1. 다음은 변수를 사용 하 여 새 이벤트 매개 변수를 사용 하는 예제 쿼리입니다 `_ event` .

    ``` kusto
    EventsAll
    | where Type has (_event)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    새 매개 변수가 대시보드 맨 위에 있는 매개 변수 목록에 표시 됩니다. 

1. 다른 값을 선택 하 여 시각적 개체를 업데이트 합니다.

### <a name="use-the-multiple-selection-query-based-parameter"></a>다중 선택 쿼리 기반 매개 변수 사용

쿼리 기반 매개 변수 값은 사용자 지정 쿼리를 실행 하 여 대시보드 로드 시에 파생 됩니다. 다음 예에서는 다중 선택 쿼리 기반 매개 변수를 만들 수 있는 방법을 보여 줍니다.

#### <a name="create-a-parameter"></a>매개 변수 만들기

1. 매개 **변수를 선택 하** 여 **매개** 변수 창을 열고 **새 매개 변수**를 선택 합니다.

1. 다음 변경 내용과 함께 [단일 선택 고정 값 매개 변수 사용](#use-the-single-selection-fixed-value-parameter) 에 설명 된 대로 세부 정보를 입력 합니다.

    * **매개 변수 표시 이름**: 이벤트
    * **매개 변수 유형**: 다중 선택
    * **변수 이름**:`_events`

1. **완료** 를 클릭 하 여 매개 변수를 만듭니다.

#### <a name="use-parameters-in-the-query"></a>쿼리에서 매개 변수 사용

1. 다음 예제 쿼리에서는 변수를 사용 하 여 새 *Events* 매개 변수를 사용 합니다 `_events` .

    ``` kusto
    EventsAll
    | where Type in (_event) or isempty(_events)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    > [!NOTE]
    > 이 샘플에서는 함수를 사용 하 여 빈 값을 확인 하는 방법으로 **모두 선택** 옵션을 사용 합니다 `isempty()` .

    새 매개 변수가 대시보드 맨 위에 있는 매개 변수 목록에 표시 됩니다. 

1. 시각적 개체를 업데이트 하려면 하나 이상의 다른 값을 선택 합니다.
