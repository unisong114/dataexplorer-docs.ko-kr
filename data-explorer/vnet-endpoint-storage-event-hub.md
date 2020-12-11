---
title: 이벤트 허브 및 Azure Storage와 같은 데이터 연결에서 사용 되는 리소스에 대 한 개인 또는 서비스 끝점을 만듭니다.
description: 이벤트 허브 및 저장소와 같은 데이터 연결에서 사용 하는 리소스에 대해 개인 또는 서비스 끝점을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gunjand
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: a8e351dc04b77a41dd7ab793581a1f464f181f4e
ms.sourcegitcommit: 724d3a3c817867b17a5a5853b6433818cbc97cf7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97050521"
---
# <a name="create-a-private-or-service-endpoint-to-event-hub-and-azure-storage"></a>Event Hub 및 Azure Storage에 대 한 개인 또는 서비스 엔드포인트 만들기

[VNet (azure Virtual Network)](/azure/virtual-network/virtual-networks-overview) 을 사용 하면 여러 유형의 azure 리소스가 서로 안전 하 게 통신할 수 있습니다. Azure [개인 링크](/azure/private-link/) 를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure 서비스 및 azure에서 호스트 하는 고객 소유/파트너 서비스에 액세스할 수 있습니다. [개인 끝점](/azure/private-link/private-endpoint-overview) 은 azure 서비스에 대 한 VNet 주소 공간의 IP 주소를 사용 하 여 azure 데이터 탐색기와 Azure Storage 및 이벤트 허브와 같은 azure 서비스 간에 안전 하 게 연결 합니다. Azure 데이터 탐색기는 Microsoft 백본을 통해 저장소 계정 또는 Event Hubs의 개인 끝점에 액세스 하 고, 데이터 내보내기, 외부 테이블 및 데이터 수집과 같은 모든 통신은 개인 IP 주소를 통해 수행 됩니다. 

개인 끝점과 달리 [서비스 끝점](/azure/virtual-network/virtual-network-service-endpoints-overview) 은 공개적으로 라우팅할 수 있는 IP 주소를 유지 합니다. VNet (Virtual Network) 서비스 끝점은 Azure 백본 네트워크를 통해 최적화 된 경로를 통해 Azure 서비스에 대 한 안전한 직접 연결을 제공 합니다. 

이 문서에서는 Azure 데이터 탐색기와 [이벤트 허브](ingest-data-event-hub-overview.md) 또는 [Azure Storage](/azure/storage/)간에 연결을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Azure 데이터 탐색기 서브넷](vnet-deployment.md)

## <a name="private-endpoint"></a>Private Endpoint

Azure [개인 끝점](/azure/private-link/private-endpoint-overview) 은 Azure [개인 링크](/azure/private-link/)를 통해 제공 되는 서비스에 비공개로 안전 하 게 연결 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 프라이빗 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 
 
# <a name="azure-storage---private-endpoint"></a>[Azure Storage-개인 끝점](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>개인 끝점을 사용 하 여 Azure 데이터 탐색기 서브넷에서 Azure Storage 계정에 대 한 액세스 허용

Azure Storage 계정에서 개인 끝점을 만드는 방법에 대 한 자습서는 [자습서: Azure 개인 끝점을 사용 하 여 저장소 계정에 연결](/azure/private-link/tutorial-private-endpoint-storage-portal)을 참조 하세요.

이 자습서에서 Azure 데이터 탐색기 서브넷이 있는 VNet 및 Azure 데이터 탐색기 서브넷을 선택 합니다.

# <a name="event-hub---private-endpoint"></a>[이벤트 허브-개인 끝점](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>개인 끝점을 사용 하 여 Azure 데이터 탐색기 서브넷에서 Azure Event Hub에 대 한 액세스 허용

이벤트 허브에서 개인 끝점을 만드는 방법에 대 한 자습서는 [Azure Portal를 사용 하 여 개인 끝점 추가](/azure/event-hubs/private-link-service#add-a-private-endpoint-using-azure-portal)를 참조 하세요.

이 자습서에서 Azure 데이터 탐색기 서브넷이 있는 VNet 및 Azure 데이터 탐색기 서브넷을 선택 합니다.

---

## <a name="service-endpoint"></a>서비스 엔드포인트

VNet (Virtual Network) [서비스 끝점](/azure/virtual-network/virtual-network-service-endpoints-overview) 은 azure 백본 네트워크를 통해 최적화 된 경로를 통해 azure 서비스에 대 한 안전한 직접 연결을 제공 합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 

# <a name="azure-storage---service-endpoint"></a>[Azure Storage-서비스 끝점](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>서비스 끝점을 사용 하 여 Azure 데이터 탐색기 서브넷에서 Azure Storage 계정에 대 한 액세스 허용

이 섹션에서는 Azure Portal를 사용 하 여 가상 네트워크 서비스 끝점을 추가 하는 방법을 보여 줍니다. 액세스를 제한 하려면이 Azure Storage 계정에 대 한 가상 네트워크 서비스 엔드포인트를 통합 합니다.

### <a name="add-a-virtual-network"></a>가상 네트워크 추가 

1. 보호하려는 스토리지 계정으로 이동합니다.
1. 왼쪽 메뉴에서 **방화벽 및 가상 네트워크** 를 선택 합니다.
1. **선택한 네트워크** 에서 액세스를 사용 하도록 설정 합니다.
1. **가상 네트워크** 에서 **+ 기존 가상 네트워크 추가** 를 선택 합니다. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-existing-vnet.png" alt-text="Azure 데이터 탐색기에 기존 VNet 연결 Azure Storage 추가":::

### <a name="add-networks-pane"></a>네트워크 창 추가

:::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-networks.png" alt-text="Azure Storage 계정에 가상 네트워크를 추가 하 여 Azure 데이터 탐색기에 연결":::

1. 오른쪽 **네트워크 추가** 창에서 Azure 구독을 선택 합니다.

1. 가상 네트워크 목록에서 가상 네트워크를 선택 하 고 서브넷을 선택 합니다. 

    > [!NOTE]
    > 목록에 가상 네트워크를 추가 하기 전에 서비스 끝점을 사용 하도록 설정 합니다. 서비스 엔드포인트를 사용하지 않는 경우 포털에서는 설정하라는 메시지가 나타납니다.
    
1. **추가** 를 선택합니다.

### <a name="save-and-verify-virtual-network-settings"></a>가상 네트워크 설정 저장 및 확인

1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-virtual-network.png" alt-text="Azure 데이터 탐색기에 저장소 계정을 연결 하는 Vnet":::

    확인이 포털 알림에 표시 될 때까지 몇 분 정도 기다립니다.

# <a name="event-hub---service-endpoint"></a>[이벤트 허브-서비스 끝점](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>서비스 끝점을 사용 하 여 Azure 데이터 탐색기 서브넷에서 Azure Event Hub에 대 한 액세스 허용

> [!IMPORTANT]
> 가상 네트워크는 Event Hubs **표준** 및 **전용** 계층에서 지원 되며 기본 계층에서는 지원 되지 않습니다. 

### <a name="add-a-virtual-network"></a>가상 네트워크 추가

1. Azure Portal에서 보안을 유지할 **Event Hubs 네임 스페이스로** 이동 합니다.
1. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다. 이 탭은 **표준** 네임 스페이스 또는 **전용** 네임 스페이스에만 표시 됩니다.
1. **방화벽 및 가상 네트워크** 탭을 선택 합니다. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/networking.png" alt-text="이벤트 허브의 네트워킹":::

1. **선택한 네트워크** 에서 액세스를 사용 하도록 설정 합니다.
1. **가상 네트워크** 에서 **+ 기존 가상 네트워크 추가** 를 선택 합니다. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-add-existing-vnet.png" alt-text="Azure 데이터 탐색기의 기존 가상 네트워크 추가":::

### <a name="add-networks-pane"></a>네트워크 창 추가

:::image type="content" source="media/vnet-private-link-storage-event-hub/add-networks.png" alt-text="Azure 데이터 탐색기에 VNet을 연결 하는 네트워크 필드 추가":::  

1. 오른쪽 **네트워크 추가** 창에서 Azure 구독을 선택 합니다.

1. 가상 네트워크 목록에서 가상 네트워크를 선택 하 고 서브넷을 선택 합니다. 목록에 가상 네트워크를 추가 하기 전에 서비스 끝점을 사용 하도록 설정 해야 합니다. 
    > [!NOTE]
    > 목록에 가상 네트워크를 추가 하기 전에 서비스 끝점을 사용 하도록 설정 합니다. 서비스 엔드포인트를 사용하지 않는 경우 포털에서는 설정하라는 메시지가 나타납니다.
1. **추가** 를 선택합니다.

### <a name="save-and-verify-virtual-network-settings"></a>가상 네트워크 설정 저장 및 확인

1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 확인이 포털 알림에 표시 될 때까지 몇 분 정도 기다립니다.
    
    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-firewalls-and-vnet.png" alt-text="Azure 데이터 탐색기에 연결 하려면 Event Hub의 가상 네트워크 및 서브넷을 추가 합니다."::: 

---

## <a name="next-steps"></a>다음 단계

* [저장소로 데이터 내보내기](kusto/management/data-export/export-data-to-storage.md)
* [Event Hub에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)
