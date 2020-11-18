---
title: 한 번 클릭 수집을 사용 하 여 이벤트 허브에서 Azure 데이터 탐색기로 데이터를 수집 합니다.
description: 이 문서에서는 한 번의 클릭 환경을 사용 하 여 이벤트 허브에서 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 6f7b33941ca38f80249808d14514faa2378c61fb
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94715029"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-data-explorer"></a>단일 클릭 수집을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-hub.md)
> * [한 번 클릭](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-hub-resource-manager.md)

Azure 데이터 탐색기에서는 빅 데이스트리터 밍 플랫폼이자 이벤트 수집 서비스인 이벤트 허브에서 데이터를 수집(로드)하는 기능을 제공합니다. [Event Hubs](/azure/event-hubs/event-hubs-about)에서는 초당 수백만 개의 이벤트를 거의 실시간으로 처리할 수 있습니다. 이 문서에서는 [한 번의 클릭](ingest-data-one-click.md) 으로 수집 환경을 사용 하 여 Azure 데이터 탐색기의 테이블에 이벤트 허브를 연결 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)
* 수집 [에 대 한 데이터를 포함 하는 이벤트 허브](ingest-data-event-hub.md#create-an-event-hub)

## <a name="ingest-new-data"></a>새 데이터 수집

1. [웹 UI](https://dataexplorer.azure.com/)의 왼쪽 메뉴에서 *데이터베이스* 또는 *테이블* 을 마우스 오른쪽 단추로 클릭 하 고 **새 데이터 수집** 을 선택 합니다. 

:::image type="content" source="media/one-click-event-hub/one-click-ingestion-in-web-ui.png" alt-text="웹 UI에서 원클릭 수집 선택":::

**원본** 탭이 선택 된 상태로 **새 데이터 수집** 창이 열립니다.

:::image type="content" source="media/one-click-event-hub/reference-to-event-hub.png" alt-text="원본 탭의 스크린샷에는 새 데이터를 Azure 데이터 탐색기에 수집 하 고 원본 = 이벤트 허브에 대 한 참조가 있습니다.":::

1. 데이터베이스 **필드는** 데이터베이스에 자동으로 채워집니다. 드롭다운 메뉴에서 다른 데이터베이스를 선택할 수 있습니다.

1. **테이블** 에서 **새로 만들기** 를 선택 하 고 새 테이블의 이름을 입력 하거나 기존 테이블을 사용 합니다. 

    > [!NOTE]
    > 테이블은 1~1024자여야 합니다. 영숫자, 하이픈 및 밑줄을 사용할 수 있습니다. 특수 문자는 지원되지 않습니다.

1. **원본 유형** 에서 **이벤트 허브에 대 한 참조** 를 선택 합니다. 데이터 연결 선택이 표시 됩니다.

## <a name="data-connection"></a>데이터 연결

1. **데이터 연결** 에서 다음 필드를 입력 합니다.

    :::image type="content" source="media/one-click-event-hub/project-details.png" alt-text="한 번의 클릭 환경에서 이벤트 허브를 사용 하 여 Azure 데이터 탐색기에 새 데이터를 수집 하는 프로젝트 세부 정보 필드를 포함 하는 소스 탭의 스크린샷":::

    |**설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 데이터 연결 이름 | *TestDataConnection*  | 데이터 연결을 식별 하는 이름입니다.
    | 구독 |      | 이벤트 허브 리소스가 있는 구독 ID입니다.  |
    | 이벤트 허브 네임스페이스 |  | 네임 스페이스를 식별 하는 이름입니다. |
    | 이벤트 허브 |  | 사용할 이벤트 허브입니다. |
    | 소비자 그룹 |  | 이벤트 허브에 정의 된 소비자 그룹입니다. |
    | 데이터 형식 | | [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체의 형태로 이벤트 허브에서 데이터를 읽습니다. 지원 되는 형식은 CSV, JSON, PSV, SCsv, SOHsv TSV 및 TSVE입니다. |
    | 이벤트 시스템 속성 | 관련 속성 선택 | [이벤트 허브 시스템 속성](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations)입니다. 이벤트 메시지 마다 여러 레코드가 있는 경우 시스템 속성이 첫 번째 속성에 추가 됩니다. 시스템 속성을 추가 하는 경우 선택한 속성을 포함 하도록 테이블 스키마 및 [매핑을](kusto/management/mappings.md) [만들거나](kusto/management/create-table-command.md) [업데이트](kusto/management/alter-table-command.md) 합니다. |

1. **스키마 편집** 을 선택 합니다.

## <a name="schema-tab"></a>스키마 탭

JSON 형식 데이터를 사용 하는 스키마 매핑에 대 한 자세한 내용은 [스키마 편집](one-click-ingestion-existing-table.md#edit-the-schema)을 참조 하세요.
CSV 형식의 데이터를 사용한 스키마 매핑에 대 한 자세한 내용은 [스키마 편집](one-click-ingestion-new-table.md#edit-the-schema)을 참조 하세요.

:::image type="content" source="media/one-click-event-hub/schema-tab.png" alt-text="한 번의 클릭 환경에서 이벤트 허브를 사용 하 여 Azure 데이터 탐색기에 새 데이터 수집의 스키마 탭 스크린샷":::

1. 미리 보기 창에 표시 되는 데이터가 완전 하지 않은 경우 필요한 데이터 필드가 모두 포함 된 테이블을 만들기 위해 더 많은 데이터가 필요할 수 있습니다. 다음 명령을 사용 하 여 이벤트 허브에서 새 데이터를 가져옵니다.
    * **새 데이터 삭제 및 페치**: 표시 된 데이터를 삭제 하 고 새 이벤트를 검색 합니다.
    * **추가 데이터 페치**: 이미 발견 된 이벤트 외에도 더 많은 이벤트를 검색 합니다. 
    
    > [!NOTE]
    > 데이터의 미리 보기를 보려면 이벤트 허브에서 이벤트를 전송 해야 합니다.
        
1. 수집 **시작** 을 선택 합니다.

## <a name="complete-data-ingestion"></a>데이터 수집 완료

데이터 수집 **완료** 창에서 데이터 수집이 성공적으로 완료 되 면 모든 단계가 녹색 확인 표시로 표시 됩니다. 이러한 단계를 수행 하는 카드는 **빠른 쿼리** 를 사용 하 여 데이터를 탐색 하거나 **도구** 를 사용 하 여 변경 내용을 실행 취소 하거나 이벤트 허브 연결 및 데이터를 **모니터링** 하는 옵션을 제공 합니다.

:::image type="content" source="media/one-click-event-hub/data-ingestion-completed.png" alt-text="한 번의 클릭 환경을 사용 하 여 이벤트 허브에서 Azure 데이터 탐색기에 수집 하는 마지막 화면의 스크린샷":::

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용하여 Azure Data Explorer에 대한 쿼리 작성](write-queries.md)
