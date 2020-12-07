---
title: Azure Data Explorer 대시보드를 사용하여 데이터 시각화
description: Azure Data Explorer 대시보드를 사용하여 데이터를 시각화하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/26/2020
ms.localizationpriority: high
ms.openlocfilehash: 23bd17b54a1910633baabf2b78a1015f31eead5a
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512777"
---
# <a name="visualize-data-with-azure-data-explorer-dashboardspreview"></a>Azure Data Explorer 대시보드를 사용하여 데이터 시각화(미리 보기)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 쿼리를 실행하고 대시보드를 작성할 수 있는 웹 애플리케이션을 제공합니다. 대시보드는 독립 실행형 웹 애플리케이션인 [웹 UI](web-query-data.md)에서 사용할 수 있습니다. 또한 Azure Data Explorer는 [Power BI](power-bi-connector.md) 및 [Grafana](grafana.md)와 같은 다른 대시보드 서비스와 통합됩니다.

Azure Data Explorer 대시보드에서 제공하는 세 가지 주요 이점은 다음과 같습니다.

* 기본적으로 쿼리를 웹 UI에서 Azure Data Explorer 대시보드로 내보냅니다. 
* 웹 UI에서 데이터를 검색합니다.
* 대시보드 렌더링 성능이 최적화되어 있습니다.

다음 이미지에서는 Azure Data Explorer 대시보드를 보여 줍니다.

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="최종 대시보드":::

> [!IMPORTANT]
> 데이터를 안전하게 보호합니다. 사용자에 대한 대시보드 및 대시보드 관련 메타데이터가 저장 상태에서 암호화됩니다.

## <a name="create-a-dashboard"></a>대시보드 만들기

1. 탐색 모음에서 **대시보드**, **새 대시보드** 를 차례로 선택합니다.

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="새 대시보드":::

1. 대시보드 이름 및 **만들기** 를 선택합니다.

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="대시보드 만들기":::

## <a name="add-data-source"></a>데이터 원본 추가

대시보드에 필요한 데이터 원본을 추가합니다.

1. 위쪽 표시줄에서 **데이터 원본** 메뉴 항목을 선택합니다. **데이터 원본** 창에서 **+ 새 데이터 원본** 단추를 선택합니다.

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="데이터 원본":::

1. **새 데이터 원본 만들기** 창에서 다음을 수행합니다.
    1. **클러스터 URI** 또는 지역이 포함된 부분 이름을 입력하고, **연결** 을 선택합니다. 
    1. 드롭다운 목록에서 **데이터베이스** 를 선택합니다.
    1. 필요한 경우 기본값을 사용하거나 **데이터 원본 이름** 을 수정합니다. 
    1. **적용** 을 선택합니다.

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="데이터 원본 창":::

## <a name="use-parameters"></a>매개 변수 사용

매개 변수를 사용하면 대시보드 필터를 사용할 수 있습니다. 매개 변수를 사용하는 경우 대시보드 렌더링 성능이 크게 향상되고 쿼리에서 필터 값을 최대한 빨리 사용할 수 있습니다. 매개 변수를 사용하는 방법에 대한 자세한 내용은 [Azure Data Explorer 대시보드에서 매개 변수 사용](dashboard-parameters.md)을 참조하세요.

1. 위쪽 표시줄에서 **매개 변수** 를 선택합니다. **매개 변수** 창에서 **+ 새 매개 변수** 단추를 선택합니다.

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="새 매개 변수 선택":::

1. 모든 필수 필드에 대한 값을 입력하고, **완료** 를 선택합니다.

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="매개 변수 창":::

|필드  |설명 |
|---------|---------|
|**매개 변수 표시 이름**    |   대시보드 또는 편집 카드에 표시되는 매개 변수의 이름입니다.      |
|**매개 변수 형식**    |다음 중 하나<ul><li>**단일 선택**: 필터에서 매개 변수에 대한 입력으로 하나의 값만 선택할 수 있습니다.</li><li>**다중 선택**: 필터에서 매개 변수에 대한 입력으로 하나 이상의 값을 선택할 수 있습니다.</li><li>**시간 범위**: 시간을 기준으로 쿼리 및 대시보드를 필터링하는 추가 매개 변수를 만들 수 있습니다. 모든 대시보드에는 기본적으로 시간 범위 선택기가 있습니다.</li></ul>    |
|**변수 이름**     |   쿼리에서 사용할 매개 변수의 이름입니다.      |
|**데이터 형식**    |    매개 변수 값의 데이터 형식입니다.     |
|**대시보드 필터로 고정**   |   매개 변수 기반 필터를 대시보드에 고정하거나 대시보드에서 고정 해제합니다.       |
|**원본**     |    매개 변수 값의 원본은 다음과 같습니다. <ul><li>**고정 값**: 수동으로 도입된 정적 필터 값입니다. </li><li>**쿼리**: KQL 쿼리를 사용하여 동적으로 도입된 값입니다.  </li></ul>    |
|**"모두 선택" 값 추가**    |   단일 선택 및 다중 선택 매개 변수 유형에만 적용됩니다. 모든 매개 변수 값에 대한 데이터를 검색하는 데 사용됩니다.      |

## <a name="add-query"></a>쿼리 추가

**쿼리 추가** 는 Kusto 쿼리 언어 코드 조각을 사용하여 데이터를 검색하고 시각적 개체를 렌더링합니다. 각 쿼리에서 단일 시각적 개체를 지원할 수 있습니다.

1. 대시보드 캔버스 또는 위쪽 메뉴 모음에서 **쿼리 추가** 를 선택합니다.

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="새 쿼리":::

1. **쿼리** 창에서 다음을 수행합니다. 
    1. 드롭다운에서 데이터 원본을 선택합니다.
    1. 쿼리를 입력하고, **실행** 을 선택합니다. 
    1. **+ 시각적 개체 추가** 를 선택합니다.

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="쿼리 실행":::

1. **시각적 개체 서식 지정** 창에서 **차트 종류** 를 선택하여 시각적 개체의 유형을 선택합니다. 
1. 시각적 개체의 이름을 지정하고, **변경 내용 적용** 을 선택하여 시각적 개체를 대시보드에 고정합니다.

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="쿼리에 시각적 개체 추가":::

1. 시각적 개체의 크기를 조정하고, **변경 내용 저장** 을 사용하여 대시보드를 저장할 수 있습니다.

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="대시보드 저장":::

## <a name="share-dashboards"></a>대시보드 공유

공유 메뉴를 사용하여 대시보드에 [권한을 부여](#grant-permissions)하고, [사용자 권한 수준을 변경](#change-a-user-permission-level)하고, [대시보드 링크를 공유](#share-the-dashboard-link)합니다.

> [!IMPORTANT]
> 대시보드에 액세스하려면 대시보드 뷰어에 다음이 필요합니다.
> * 액세스를 위한 대시보드 링크
> * 대시보드 권한
> * Azure Data Explorer 클러스터의 기본 데이터베이스에 대한 액세스  

1. 대시보드의 위쪽 표시줄에서 **공유** 메뉴 항목을 선택합니다.
1. 드롭다운에서 **권한 관리** 를 선택합니다. 

    :::image type="content" source="media/adx-dashboards/share-dashboard.png" alt-text="대시보드 공유 드롭다운":::

### <a name="grant-permissions"></a>권한 부여

**대시보드 권한** 창에서 사용자에게 권한을 부여하려면 다음을 수행합니다.
1. **새 멤버 추가** 상자에서 사용자의 이름 또는 이메일을 작성합니다.
1. **권한** 수준을 **보기 가능** 또는 **편집 가능** 으로 선택한 다음, **추가** 를 클릭합니다.

:::image type="content" source="media/adx-dashboards/dashboard-permissions.png" alt-text="대시보드 권한 관리":::

### <a name="change-a-user-permission-level"></a>사용자 권한 수준 변경

**대시보드 권한** 창에서 사용자 권한 수준을 변경하려면 다음을 수행합니다.
1. 검색 상자를 사용하거나 사용자 목록을 스크롤하여 사용자를 찾습니다.
1. 필요에 따라 **권한** 수준을 변경합니다.

### <a name="share-the-dashboard-link"></a>대시보드 링크 공유

대시보드 링크를 공유하려면 다음을 수행합니다.
* **공유** 드롭다운을 선택한 다음, **링크 복사** 를 선택합니다. 또는
* **대시보드 권한** 창에서 **링크 복사** 를 선택합니다. 

## <a name="enable-auto-refresh"></a>자동 새로 고침 사용 

1. 대시보드 메뉴에서 **편집** 을 선택하여 편집 모드로 전환합니다.
1. **자동 새로 고침** 을 선택합니다. 
 
    :::image type="content" source="media/adx-dashboards/auto-refresh.png" alt-text="자동 새로 고침 선택":::

1. 자동 새로 고침이 **사용** 이 되도록 옵션을 전환합니다. 
1. **최소 시간 간격** 및 **기본 새로 고침 빈도** 에 대한 값을 선택합니다. 

    :::image type="content" source="media/adx-dashboards/auto-refresh-toggle.png" alt-text="자동 새로 고침 사용":::

1. **적용** 을 선택하고, 대시보드를 **저장** 합니다.

> [!NOTE]
> * 클러스터에 대한 불필요한 부하를 줄이려면 최소 시간 간격을 선택합니다. 
> * 대시보드 뷰어: 
>    * 최소 시간 간격은 개인 용도로만 변경할 수 있습니다. 
>    * 편집기에서 지정한 **최소 시간 간격** 보다 작은 값은 선택할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer 대시보드에서 매개 변수 사용](dashboard-parameters.md)
* [대시보드 시각적 개체 사용자 지정](dashboard-customize-visuals.md)
* [Azure Data Explorer에서 데이터 쿼리](web-query-data.md)
