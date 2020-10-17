---
title: 저장소 계정에 Event Grid 구독 만들기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 저장소 계정에 Event Grid 구독을 만드는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 51d5c48fbcd2373ed5cdd1973cc9e53abb26b2de
ms.sourcegitcommit: 58588ba8d1fc5a6adebdce2b556db5bc542e38d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92099554"
---
# <a name="manually-create-resources-for-event-grid-ingestion"></a>Event Grid 수집을 위한 리소스를 수동으로 만듭니다.

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-grid.md)
> * [포털-수동으로 리소스 만들기](ingest-data-event-grid-manual.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md)

Azure 데이터 탐색기는 [Event Grid 수집 파이프라인](ingest-data-event-grid-overview.md)을 사용 하 여 Azure Storage (azure Blob Storage 및 Azure Data Lake Storage Gen2)에서 지속적인 수집을 제공 합니다. Event Grid 수집 파이프라인에서 Azure Event Grid 서비스는 Azure 이벤트 허브를 통해 저장소 계정에서 Azure 데이터 탐색기로 만들었거나 blob의 이름을 바꾼 이벤트를 라우팅합니다.

이 문서에서는 Event Grid 구독, 이벤트 허브 네임 스페이스 및 이벤트 허브를 Event Grid 수집 하는 데 필요한 리소스를 수동으로 만드는 방법에 대해 알아봅니다. 이벤트 허브 네임 스페이스 및 이벤트 허브 만들기는 [필수 구성 요소](#prerequisites)에 설명 되어 있습니다. Event Grid 수집을 정의 하는 동안 이러한 리소스의 자동 생성을 사용 하려면 [Azure 데이터 탐색기에서 Event Grid 데이터 연결 만들기](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 [무료 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [저장소 계정](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
    * `BlobStorage`, `StorageV2` 또는 [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction)에 대 한 Azure Storage 계정에 Event Grid 알림 구독을 설정할 수 있습니다.
* [이벤트 허브 네임 스페이스 및 이벤트 허브](/azure/event-hubs/event-hubs-create)

> [!NOTE]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

## <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기
 
1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. 왼쪽 메뉴에서 **이벤트**  >  **이벤트 구독**을 선택 합니다.

     :::image type="content" source="media/eventgrid/create-event-grid-subscription-1.png" alt-text="Event grid 구독 만들기":::

1. **기본** 탭의 **이벤트 구독 만들기** 창에 다음 값을 제공합니다.

    :::image type="content" source="media/eventgrid/create-event-grid-subscription-2.png" alt-text="Event grid 구독 만들기":::

    |**설정** | **제안 값** | **필드 설명**|
    |---|---|---|
    | Name | *test-grid-connection* | 만들려는 event grid 구독의 이름입니다.|
    | 이벤트 스키마 | *Event Grid 스키마* | Event Grid에 사용해야 하는 스키마입니다. |
    | 항목 종류 | *스토리지 계정* | Event Grid 항목의 형식입니다. 자동으로 채워집니다.|
    | 원본 리소스 | *gridteststorage1* | 사용자 스토리지 계정의 이름입니다. 자동으로 채워집니다.|
    | 시스템 토픽 이름 | *gridteststorage1...* | Azure Storage 이벤트를 게시 하는 시스템 항목입니다. 그런 다음이 시스템 항목은 이벤트를 수신 하 고 처리 하는 구독자로 이벤트를 전달 합니다. 자동으로 채워집니다.|
    | 이벤트 유형으로 필터링 | *만든 Blob* | 알림을 받을 특정 이벤트 구독을 만들 때 현재 지원 되는 유형 중 하나를 선택 합니다. 또는 BlobRenamed|

1. **끝점 세부 정보**에서 **Event Hubs**를 선택 합니다.

    :::image type="content" source="media/eventgrid/endpoint-details.png" alt-text="Event grid 구독 만들기":::

1. **끝점 선택** 을 클릭 하 고 만든 이벤트 허브를 입력 합니다 (예: *test-hub)*.
    
1. 특정 주제를 추적 하려면 **필터** 탭을 선택 합니다. 알림에 대한 필터를 다음과 같이 설정합니다.
   
    :::image type="content" source="media/eventgrid/filters-tab.png" alt-text="Event grid 구독 만들기":::

   1. **제목 필터링 사용**을 선택합니다.
   1. **제목 시작** 필드는 주체의 *리터럴* 접두사입니다. 적용 되는 패턴은 *startswith*이므로 여러 컨테이너, 폴더 또는 blob에 걸쳐 있을 수 있습니다. 와일드카드는 허용되지 않습니다.
       * Blob 컨테이너에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *`/blobServices/default/containers/[container prefix]`* 합니다.
       * Blob 접두사 (또는 Azure Data Lake Gen2의 폴더)에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* 합니다..
   1. **제목 종료 문자** 필드는 Blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다.
   1. **대/소문자 구분 주체 일치** 필드 접두사 및 접미사 필터에서 대/소문자를 구분 하는지 여부를 나타냅니다.

    이벤트를 필터링 하는 방법에 대 한 자세한 내용은 [blob storage 이벤트](/azure/storage/blobs/storage-blob-event-overview#filtering-events)를 참조 하세요.

1. **만들기**

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 Azure Portal: Event Grid 데이터 연결 만들기](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)를 통해 설치를 계속 하 고 azure 데이터 탐색기에 대 한 데이터 수집 연결을 만듭니다.

* 만든 리소스를 사용 하 여 Event Grid 수집을 계속 하지 않고 리소스를 더 이상 사용 하지 않으려면 [리소스를 정리](ingest-data-event-grid.md#clean-up-resources) 하 여 비용 발생을 방지 합니다.
