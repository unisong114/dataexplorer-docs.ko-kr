---
title: Redash를 사용하여 Azure 데이터 탐색기 시각화
description: 이 문서에서는 Redash 네이티브 커넥터를 사용하여 Azure 데이터 탐색기의 데이터를 시각화하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8237eb917f5123755898d281151ce34a08eb90db
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495459"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Redash의 Azure 데이터 탐색기에서 데이터 시각화

[Redash는](https://redash.io/) 데이터 원본을 연결하고 쿼리하고 대시보드를 빌드하여 데이터를 시각화하고 동료와 공유합니다. 이 문서에서는 Azure Data Explorer를 Redash의 데이터 원본으로 설정한 다음 데이터를 시각화하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md).
1. 샘플 데이터를 Azure [데이터 탐색기로 수집할 때](ingest-sample-data.md)설명한 대로 데이터 수집. 자세한 내용은 [인기 개요를](ingest-data-overview.md)참조하세요.

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Redash에서 Azure 데이터 탐색기 커넥터 만들기 

1. [Redash에](https://www.redash.io/)로그인합니다. 계정 **만들기를** 선택합니다.
1. 시작하겠습니다에서 데이터 원본 **연결을** **선택합니다.**

    ![데이터 원본에 연결](media/redash/connect-data-source.png)

1. **새 데이터 원본 만들기** 창에서 Azure 데이터 **탐색기(Kusto)를**선택한 다음 **을 선택합니다.** 

    ![Azure 데이터 탐색기 데이터 원본 선택](media/redash/select-adx-data-source.png)

1. **Kusto(Azure 데이터 탐색기)** 창에서 다음 양식을 완료하고 **만들기를**선택합니다.

    ![Azure 데이터 탐색기(쿠스토) 설정 창](media/redash/adx-settings-window.png)

1. **설정** 창에서 **연결 저장** 및 **테스트** 를 선택하여 Azure **데이터 탐색기(Kusto)** 데이터 원본 연결을 테스트합니다.

## <a name="create-queries-in-redash"></a>Redash에서 쿼리 만들기

1. Redash의 왼쪽 상단에**쿼리** **만들기를** > 선택합니다. 새 **쿼리를** 클릭하고 쿼리 이름을 바꿉니다.

    ![쿼리 만들기](media/redash/create-query.png)

1. 상단 편집 창에 쿼리를 입력하고 **저장** 및 **실행을**선택합니다. 나중에 사용할 수 있도록 쿼리를 게시하려면 **게시를** 선택합니다.

    ![쿼리 저장 및 실행](media/redash/save-and-execute-query.png)

    왼쪽 창에서 드롭다운 메뉴에서 데이터 원본 연결 이름(흐름의**Github 커넥터)과** 선택한 데이터베이스의 테이블을 볼 수 있습니다. 

1. 하단 중앙 창에서 쿼리 결과를 봅니다. 새 시각화 단추를 선택하여 쿼리와 함께 진행할 **시각화를** 만듭니다.

    ![새로운 시각화](media/redash/new-visualization.png)

1. 시각화 화면에서 **시각화 유형** 및 X 열 및 **Y 열과**같은 관련 필드를 선택합니다. **X Column** 시각화를 **저장합니다.**

    ![시각화 구성 및 저장](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>매개 변수를 사용하여 쿼리 만들기

1. **Create** > **쿼리를** 만들어 새 쿼리를 만듭니다. {}{}곱슬 대괄호를 사용하여 매개 변수를 추가합니다. **{}를{}** 선택하여 매개 변수 추가 창을 **엽니다.** *설정 아이콘을* 선택하여 기존 매개 변수의 속성을 수정하고 **<parameter_name>** 창을 열 수도 있습니다. 

    ![삽입 매개변수](media/redash/insert-parameter.png)

1. 매개 변수의 이름을 지정합니다. **유형**: 드롭다운 메뉴에서 **쿼리 기반 드롭다운 목록을** 선택합니다. **확인**을 선택합니다.

    ![쿼리 기반 드롭다운 목록](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 쿼리는 여러 값을 사용하므로 다음 구문을 `| where Type in ((split('{{Type}}', ',')))`포함해야 합니다. 자세한 내용은 [연산자 를](kusto/query/inoperator.md)참조하십시오. 이로 인해 [redash 앱에서 여러 쿼리 매개 변수 옵션이](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) 생성됩니다.

## <a name="create-a-dashboard-in-redash"></a>Redash에서 대시보드 만들기

1. 대시보드를 만들려면**대시보드**를 **만듭니다.** >  또는 기존 대시보드, **대시보드** > 목록에서 대시보드를 선택합니다.

    ![대시보드 만들기](media/redash/create-dashboard.png)

1. **새 대시보드** 창에서 대시보드 이름을 지정하고 **저장을**선택합니다. **>창에서<Dashboard_name** **위젯 추가를** 선택하여 새 위젯을 만듭니다. 

1. **위젯 추가** 창에서 쿼리 이름 선택, **시각화 선택**및 **매개 변수에서**. **대시보드에 추가** 선택

   ![시각화를 선택하고 대시보드에 추가](media/redash/add-widget-window.png)

1. 대시보드 만들기를 완료하려면 **편집 완료를** 선택합니다.

1.  대시보드 편집 모드에서 대시보드 **수준 필터 사용을** 선택하여 이전에 정의된 **유형** 매개 변수를 사용합니다.

    ![완벽한 대시보드 생성](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)


