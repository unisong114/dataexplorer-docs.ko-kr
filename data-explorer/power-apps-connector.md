---
title: Azure Data Explorer에서 데이터를 쿼리하는 Power Apps 애플리케이션 만들기
description: Azure의 데이터를 기반으로 하 여 Power Apps에서 응용 프로그램을 만드는 방법에 대해 알아봅니다 데이터 탐색기
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 17ee68efa1f7c43814c4c86f9e5881cd0ba9af61
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349548"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>Azure 데이터 탐색기에서 데이터를 쿼리 하는 Power Apps 응용 프로그램 만들기 (미리 보기)

Azure 데이터 탐색기는 응용 프로그램, 웹 사이트, IoT 장치 등에서 다량의 데이터 스트리밍을 실시간으로 분석 하기 위한 완전히 관리 되는 데이터 분석 서비스입니다.

Power Apps는 비즈니스 데이터에 연결 하는 사용자 지정 앱을 빌드하는 신속한 응용 프로그램 개발 환경을 제공 하는 응용 프로그램, 서비스, 커넥터 및 데이터 플랫폼 모음입니다. Power Apps 커넥터는 Azure 데이터 탐색기의 스트리밍 데이터 컬렉션이 크고 증가 하며이 데이터를 사용 하기 위해 낮은 코드의 매우 기능적인 앱을 빌드하는 경우 특히 유용 합니다. 이 문서에서는 Azure 데이터 탐색기 데이터를 쿼리 하는 Power Apps 응용 프로그램을 만듭니다. 이 과정에서 데이터 매개 변수화, 검색 및 프레젠테이션 단계가 표시 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* 라이선스를 Power 플랫폼 합니다. 에서 시작 하세요 [https://powerapps.microsoft.com](https://powerapps.microsoft.com) .
* [Power Apps 제품군](/powerapps/powerapps-overview)에 대해 잘 알고 있어야 합니다.

## <a name="connect-to-azure-data-explorer-connector"></a>Azure 데이터 탐색기 커넥터에 연결

1. 으로 이동 하 여 [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/) 로그인 합니다.

1. 왼쪽 메뉴에서 **연결** 을 선택 합니다.
1. **+ 새 연결** 을 선택 합니다.

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="Power Apps에서 새 연결 만들기":::

1. 검색 창에서 **Azure 데이터 탐색기** 를 검색 합니다. 결과 옵션에서 **Azure 데이터 탐색기** 를 선택 합니다.

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="Power Apps에서 Azure 데이터 탐색기 연결 검색 및 선택":::

1. **Azure 데이터 탐색기** Popup에서 **만들기** 를 선택 합니다. 필요에 따라 자격 증명을 제공 합니다.

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="Azure 데이터 탐색기에 대 한 커넥터 만들기-팝업 창":::

## <a name="create-app"></a>앱 만들기

1. Power Apps로 이동 하 여 왼쪽 메뉴에서 **앱** 을 선택 합니다.
1. 메뉴 모음에서 **+ 새 앱** 을 선택 합니다.
1. 결과 드롭다운에서 **캔버스** 를 선택 합니다.

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="Azure 데이터 탐색기에 새 앱 및 캔버스-Power Apps 커넥터 만들기":::

1. 비어 있는 **앱** 섹션에서 **태블릿 레이아웃** 을 선택 합니다.

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="태블릿 레이아웃-Power Apps 커넥터에서 Azure 데이터 탐색기 빈 캔버스로 시작":::

### <a name="add-connector"></a>커넥터 추가

1. 왼쪽 탐색에서 **데이터** 아이콘을 클릭 합니다. 
1. **커넥터** 를 확장 합니다.
1. 결과 옵션에서 **Azure 데이터 탐색기** 를 선택 합니다.

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="Power Apps에서 Azure 데이터 탐색기에 커넥터 추가":::

이제 **Azure 데이터 탐색기** 포함 된 **앱에서** 라는 새 영역이 표시 됩니다.

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="이제 Power Apps의 앱 영역에 Azure 데이터 탐색기이 표시 됩니다.":::

### <a name="save-your-app"></a>앱 저장

1. 메뉴 모음에서 **파일** 을 선택 합니다. 
1. 왼쪽 탐색에서 **저장** 을 선택 합니다.

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="앱을 Power Apps에 저장":::

1. 앱에 대 한 의미 있는 이름을 입력 합니다. 오른쪽 아래에 있는 **저장** 단추를 클릭 합니다.

### <a name="advanced-settings"></a>고급 설정

1. 왼쪽 메뉴에서 **설정** 을 선택 합니다.
1. **고급 설정** 을 선택합니다.
1. 결과 옵션에서 **동적 스키마** 를 선택 합니다. 이 기능을 사용 하도록 설정 합니다.

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="파워 앱에서 동적 스키마 설정 사용-Azure 데이터 탐색기에 연결":::

1. **비 위임 가능한 쿼리 설정의 데이터 행 제한을** 검색 합니다. 반환 된 레코드 제한을 설정 합니다.

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="Power Apps에서 반환 결과 제한 설정-Azure 데이터 탐색기":::

    > [!NOTE]
    > 기본 제한은 500 이며 반환 된 레코드는 최대 2000입니다.

> [!IMPORTANT]
> 앱을 다시 저장 하 고 필요에 따라 다시 시작 합니다.

### <a name="add-dropdown"></a>드롭다운 추가

1. 메뉴 모음에서 **삽입** 을 선택 합니다. 
1. 결과 하위 메뉴 모음에서 **입력** 을 선택 합니다. 
1. 결과 드롭다운에서 **드롭다운** 을 선택 합니다.
1. 오른쪽 팝아웃 **고급** 탭을 클릭 합니다.
1. **항목** 입력 상자를 ["캘리포니아", "MICHIGAN"]로 채웁니다.

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="드롭다운 메뉴에서 항목 채우기" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. **드롭다운** 이 선택 된 상태에서 수식 입력줄의 **속성** 드롭다운에서 **OnChange** 를 선택 합니다.

1. 다음 수식을 입력 합니다.

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. **스키마 캡처** 단추를 클릭 합니다. 처리 시간을 허용 합니다.

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="드롭다운 메뉴에서 스키마 캡처 단추를 선택 합니다.":::

### <a name="add-data-table"></a>데이터 테이블 추가

1. 메뉴 모음에서 **삽입** 을 선택 합니다. 
1. 결과 하위 메뉴 모음에서 **데이터 테이블** 을 선택 합니다.
1. 데이터 테이블의 위치를 변경 하 고 표시 유형 테두리를 추가 하는 것을 고려 합니다.
1. 오른쪽 팝아웃 **속성** 탭을 선택 합니다. **데이터 원본** 드롭다운에서 결과를 선택 합니다.
1. **필드 편집** 링크를 선택 합니다. 
1. 결과 팝아웃에서 **+ 필드 추가** 를 선택 합니다. 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="테이블 위치 변경 및 테두리 추가" lightbox="media/power-apps-connector/insert-data-table.png":::

1. 원하는 필드를 선택 하 고 **추가** 단추를 클릭 합니다. 선택한 데이터 테이블의 미리 보기가 표시 됩니다.

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="데이터로 채워진 테이블 미리 보기":::

### <a name="validate"></a>유효성 검사

1. 화면 오른쪽 위에서 **앱 미리 보기** 단추를 클릭 합니다.
1. 드롭다운을 시도 하 고 데이터 테이블을 스크롤하여 성공적으로 데이터 검색 및 프레젠테이션을 확인 합니다.

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="Azure의 데이터를 사용 하 여 Power Apps에서 새 앱 미리 보기 데이터 탐색기 ":::

### <a name="limitations"></a>제한 사항

* Power Apps는 클라이언트에 반환 되는 결과 레코드를 최대 2000 개로 제한 합니다. 이러한 레코드에 대 한 전체 메모리는 64 MB를 초과할 수 없으며 실행 하는 데 7 분이 소요 됩니다.
* 커넥터는 [포크](./kusto/query/forkoperator.md) 및 [패싯](./kusto/query/facetoperator.md) 연산자를 지원 하지 않습니다.
* **시간 제한 예외** : 커넥터에는 7 분의 제한 시간 제한이 있습니다. 잠재적 시간 제한 문제를 방지 하려면 쿼리를 더 효율적으로 실행 하 여 더 빠르게 실행 하거나 청크로 분리 합니다. 각 청크는 쿼리의 다른 부분에서 실행할 수 있습니다. 자세한 내용은 [쿼리 모범 사례](./kusto/query/best-practices.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계