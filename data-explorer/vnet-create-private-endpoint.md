---
title: 가상 네트워크의 Azure 데이터 탐색기 클러스터에서 개인 끝점 사용
description: 가상 네트워크의 Azure 데이터 탐색기 클러스터에서 개인 끝점 사용
author: orspod
ms.author: orspodek
ms.reviewer: elbirnbo
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: 7c2dfe62852b05239215f0c88c711cea4093808e
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067575"
---
# <a name="create-a-private-endpoint-in-your-azure-data-explorer-cluster-in-your-virtual-network"></a>가상 네트워크의 Azure 데이터 탐색기 클러스터에 개인 끝점을 만듭니다.

개인 끝점과 개인 링크를 사용 하 여 VNet (가상 네트워크)에서 Azure 데이터 탐색기 클러스터에 안전 하 게 액세스할 수 있습니다. 

[개인 링크 서비스](https://docs.microsoft.com/azure/private-link/private-link-service-overview)를 설정 하려면 Azure VNet 주소 공간의 IP 주소를 사용 하 여 개인 끝점을 사용 합니다. [Azure 개인 끝점](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 은 VNet의 개인 IP 주소를 사용 하 여 개인적으로 안전 하 게 azure 데이터 탐색기에 연결 합니다. 또한 개인 끝점을 사용 하 여 연결 하려면 클러스터의 [DNS 구성을](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) 다시 구성 해야 합니다. 이 설정을 사용 하면 개인 네트워크의 클라이언트와 Azure 데이터 탐색기 클러스터 간의 네트워크 트래픽이 VNet을 통해 이동 하 고 공용 인터넷에서 노출을 제거 하 여 Microsoft 백본 네트워크의 [개인 링크로](https://docs.microsoft.com/azure/private-link/) 이동 합니다. 이 문서에서는 쿼리 (엔진) 및 수집 (데이터 관리)을 위해 클러스터에서 개인 끝점을 만들고 구성 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* [가상 네트워크에서 Azure 데이터 탐색기 클러스터](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal) 만들기
* 네트워크 정책 사용 안 함:
  * Azure 데이터 탐색기 클러스터 가상 네트워크에서 [개인 링크 서비스 정책을](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)사용 하지 않도록 설정 합니다.
  * Azure 데이터 탐색기 클러스터 가상 네트워크와 동일할 수 있는 개인 끝점 가상 네트워크에서 [개인 끝점 정책을](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy)사용 하지 않도록 설정 합니다.

## <a name="create-private-link-service"></a>개인 링크 서비스 만들기

클러스터의 모든 서비스에 안전 하 게 연결 하려면 [개인 링크 서비스](https://docs.microsoft.com/azure/private-link/private-link-service-overview) 를 두 번 만들어야 합니다. 즉, 쿼리 (엔진)에 대해 한 번, 수집 (데이터 관리)에 대해 한 번입니다.

1. 포털의 왼쪽 상단 모서리에서 **+ 리소스 만들기** 단추를 선택합니다.
1. *개인 링크 서비스*를 검색 합니다.
1. **개인 링크 서비스**에서 **만들기**를 선택 합니다.

    :::image type="content" source="media/vnet-create-private-endpoint/create-service.gif" alt-text="Azure 데이터 탐색기 포털에서 개인 링크 서비스를 만드는 처음 세 단계를 보여 주는 Gif":::

1. **개인 링크 서비스 만들기** 창에서 다음 필드를 입력 합니다.

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-basics.png" alt-text="개인 링크 서비스 만들기의 탭 1-기본 사항":::

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 가상 네트워크 클러스터를 보유 하는 Azure 구독을 선택 합니다.|
    | Resource group | 리소스 그룹 | 가상 네트워크 클러스터를 보유 하는 리소스 그룹을 선택 합니다. |
    | 이름 | AzureDataExplorerPLS | 리소스 그룹에서 개인 링크 서비스를 식별 하는 이름을 선택 합니다. |
    | Azure 지역 | 가상 네트워크와 동일 | 가상 네트워크 지역과 일치 하는 지역을 선택 합니다. |

1. **아웃 바운드 설정** 창에서 다음 필드를 입력 합니다.

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-outbound.png" alt-text="개인 링크 탭 2-아웃 바운드 설정":::

    |**설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Load Balancer | 엔진 또는 *데이터 관리* Load Balancer | 클러스터 엔진에 대해 생성 된 부하 분산 장치를 선택 하 고, 엔진 공용 IP를 가리키는 부하 분산 장치를 선택 합니다.  부하 분산 장치 엔진 이름은 다음 형식으로 지정 됩니다. kucompute-{clustername}-elb <br> *Load Balancer 데이터 관리 이름은 다음 형식으로 지정 됩니다. kudatamgmt-{clustername}-elb*|
    | 부하 분산 장치 프런트 엔드 IP 주소 | 엔진 또는 데이터 관리 공용 IP입니다. | Load Balancer 공용 IP 주소를 선택 합니다. |
    | 원본 NAT 서브넷 | 클러스터의 서브넷 | 클러스터가 배포 되는 서브넷입니다.
    
1. **액세스 보안** 창에서 개인 링크 서비스에 대 한 액세스를 요청할 수 있는 사용자를 선택 합니다.
1. **검토 + 만들기** 를 선택 하 여 개인 링크 서비스 구성을 검토 합니다. **만들기** 를 선택 하 여 개인 링크 서비스를 만듭니다.
1. 개인 링크 서비스를 만든 후 리소스를 열고 다음 단계인 **개인 끝점 만들기**에 대 한 개인 링크 별칭을 저장 합니다. 예제 별칭: *AzureDataExplorerPLS-222 -333. westus. privatelinkservice*

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

클러스터의 모든 서비스에 안전 하 게 연결 하려면 [개인 끝점](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 을 쿼리 (엔진)에 대해 한 번, 수집을 위해 한 번 (데이터 관리) 두 번 만들어야 합니다.

1. 포털의 왼쪽 상단 모서리에서 **+ 리소스 만들기** 단추를 선택합니다.
1. *개인 끝점*을 검색 합니다.
1. **개인 끝점**에서 **만들기**를 선택 합니다.
1. **개인 끝점 만들기** 창에서 다음 필드를 입력 합니다.

    :::image type="content" source="media/vnet-create-private-endpoint/step-one-basics.png" alt-text="개인 끝점 양식 만들기 1 단계-기본 사항":::

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 개인 끝점에 사용 하려는 Azure 구독을 선택 합니다.|
    | Resource group | 리소스 그룹 | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 이름 | AzureDataExplorerPE | 리소스 그룹에서 가상 네트워크를 식별 하는 이름을 선택 합니다.
    | Azure 지역 | *미국 서부* | 요구에 가장 적합한 지역을 선택합니다.
    
1. **리소스** 창에서 다음 필드를 입력 합니다.

    :::image type="content" source="media/vnet-create-private-endpoint/step-two-resource.png" alt-text="가상 네트워크 폼 만들기 2 단계-리소스":::

    **설정** | **값**
    |---|---|
    | 연결 방법 | 개인 링크 서비스 별칭 |
    | 리소스 ID 또는 별칭 | 엔진 개인 링크 서비스 별칭입니다. 예제 별칭: *AzureDataExplorerPLS-222 -333. westus. privatelinkservice*  |
    
1. **검토 + 만들기** 를 선택 하 여 개인 끝점 구성을 검토 하 고 개인 끝점 서비스를 **만듭니다** .
1. 수집 (데이터 관리)을 위한 개인 끝점을 만들려면 다음과 같은 변경 내용으로 동일한 지침을 따릅니다.
    1. **리소스** 창에서 수집 (데이터 관리) 개인 링크 서비스 별칭을 선택 합니다.

> [!NOTE]
> 여러 개인 끝점에서 개인 링크 서비스에 연결할 수 있습니다.

## <a name="approve-your-private-endpoint"></a>개인 끝점 승인

> [!NOTE]
> 개인 링크 서비스를 만들 때 **액세스 보안** 창에서 *자동 승인* 옵션을 선택한 경우에는이 단계가 필수가 아닙니다.

1. 개인 링크 서비스의 설정 아래에서 **개인 끝점 연결** 을 선택 합니다.
1. 연결 목록에서 개인 끝점을 선택 하 고 **승인**을 선택 합니다.

:::image type="content" source="media/vnet-create-private-endpoint/private-link-approve.png" alt-text="개인 끝점을 만들기 위한 승인 단계"::: 

## <a name="set-dns-configuration"></a>DNS 구성 설정

Azure 데이터 탐색기 클러스터를 가상 네트워크에 배포 하는 경우 레코드 이름과 영역 호스트 이름 사이에 *privatelink* 를 사용 하 여 정식 이름을 가리키도록 [DNS 항목](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) 을 업데이트 합니다. 이 항목은 엔진 및 수집 (데이터 관리)에 대해 모두 업데이트 됩니다. 

예를 들어, 엔진 DNS 이름이 myadx.westus.kusto.windows.net 인 경우 이름 확인은 다음과 같습니다.

* **이름**: myadx.westus.kusto.windows.net   <br> **유형**: CNAME   <br> **값**: myadx.privatelink.westus.kusto.windows.net
* **이름**: myadx.privatelink.westus.kusto.windows.net   <br> **형식**: A   <br> **값**: 40.122.110.154
    > [!NOTE]
    > 이 값은 클러스터를 만들 때 사용자가 제공한 쿼리 (엔진) 공용 IP 주소입니다.

개인 DNS 서버 또는 Azure 개인 DNS 영역을 설정 합니다. 테스트의 경우 테스트 컴퓨터의 호스트 항목을 수정할 수 있습니다.

다음 DNS 영역을 만듭니다. **privatelink.region.kusto.windows.net**. 예제의 DNS 영역은 *privatelink.westus.kusto.windows.net*입니다. A 레코드와 개인 끝점 IP를 사용 하 여 엔진에 대 한 레코드를 등록 합니다.

예를 들어 이름 확인은 다음과 같습니다.

* **이름**: myadx.westus.kusto.windows.net   <br>**유형** : CNAME   <br>**값**: myadx.privatelink.westus.kusto.windows.net
* **이름**: myadx.privatelink.westus.kusto.windows.net   <br>**형식**: A   <br>**값**: 10.3.0.9
    > [!NOTE]
    > 이 값은 개인 끝점 IP입니다. IP를 쿼리 (엔진) 개인 링크 서비스에 이미 연결 했습니다.

이 DNS 구성을 설정한 후 다음 URL을 사용 하 여 Virtual Network 내부의 쿼리 (엔진)에 연결할 수 있습니다. myadx.region.kusto.windows.net.

수집 (데이터 관리)에 연결 하려면 A 레코드와 수집 개인 끝점 IP를 사용 하 여 수집 (데이터 관리)에 대 한 레코드를 등록 합니다.

## <a name="next-steps"></a>다음 단계

* [Virtual Network에 Azure 데이터 탐색기 클러스터 배포](vnet-deployment.md)
* [가상 네트워크에서 Azure 데이터 탐색기 클러스터의 액세스, 수집 및 작업 문제 해결](vnet-deploy-troubleshoot.md)
