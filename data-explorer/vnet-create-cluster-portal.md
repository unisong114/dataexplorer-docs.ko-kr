---
title: 가상 네트워크에서 & DB에 Azure 데이터 탐색기 클러스터 만들기
description: 이 문서에서는 가상 네트워크에서 Azure 데이터 탐색기 클러스터를 만드는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500802"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>가상 네트워크에서 Azure 데이터 탐색기 클러스터 만들기

Azure Data Explorer는 가상 네트워크(VNet)의 서브넷에 클러스터배포를 지원합니다. 이 기능을 사용하면 Azure 가상 네트워크 또는 온-프레미스에서 클러스터에 개인적으로 액세스하고, 가상 네트워크 내의 이벤트 허브 및 저장소와 같은 리소스에 액세스하고, 인바운드 및 아웃바운드 트래픽을 제한할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-network-security-group-nsg"></a>NSG(네트워크 보안 그룹 만들기)

[NSG(네트워크 보안 그룹)는](/azure/virtual-network/security-overview) VNet 내에서 네트워크 액세스를 제어하는 기능을 제공합니다. Azure 데이터 탐색기는 두 개의 끝점 HTTP(443) 및 TDS(1433)를 사용하여 액세스할 수 있습니다. 클러스터의 관리, 모니터링 및 적절한 운영을 위해 이러한 끝점에 액세스할 수 있도록 다음 NSG 규칙을 구성해야 합니다.

네트워크 보안 그룹을 만들려면 다음을 수행하십시오.

1. 포털의 왼쪽 위 모서리에 있는 **+리소스** 만들기 단추를 선택합니다.
1. 네트워크 *보안 그룹을 검색합니다.*
1. **네트워크 보안 그룹에서**화면 하단에서 을 **선택합니다.**
1. 네트워크 **보안 그룹 만들기** 창에서 다음 정보를 입력합니다.

   ![NSG 양식 만들기](media/vnet-create-cluster-portal/network-security-group.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 클러스터에 사용할 Azure 구독을 선택합니다.|
    | Resource group | 리소스 그룹 | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 이름 | AzureDataExplorerNsg | 리소스 그룹에서 NSG(네트워크 보안 그룹)를 식별하는 이름을 선택합니다.  |
    | 지역 | *미국 서부* | 요구에 가장 적합한 지역을 선택합니다.
    | | | |

1. **검토 + 만들기**를 선택하여 클러스터 세부 정보를 검토하고 **만들기**를 선택하여 클러스터를 프로비저닝합니다.

1. 배포가 완료되면 **리소스로 이동**을 선택합니다.

    ![리소스로 이동](media/vnet-create-cluster-portal/notification-nsg.png)

1. **인바운드 보안 규칙** 탭에서 **+Add**를 선택합니다.
1. **인바운드 보안 규칙 추가** 창에서 다음 정보를 입력합니다.

    ![NSG 인바운드 규칙 양식 만들기](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **설정** | **제안 값** 
    |---|---|
    | 원본 | 서비스 태그
    | 원본 서비스 태그 | AzureData탐색기관리
    | 원본 포트 범위 | *
    | 대상 | VirtualNetwork
    | 대상 포트 범위 | *
    | 프로토콜 | TCP
    | 작업 | Allow
    | 우선 순위 | 100
    | 이름 | 허용Azure데이터익분기관리
    | | |
    
1. [VNet 배포에 대한 종속성에](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)따라 모든 인바운드 및 아웃바운드 종속성에 대해 이전 두 단계를 반복합니다. 또는 아웃바운드 규칙을 단일 규칙으로 대체하여 포트 443 및 80에 대한 *인터넷을* 허용할 수 있습니다.
    
    인바운드 및 아웃바운드 종속성에 대한 NSG 규칙은 다음과 같아야 합니다.

    ![NSG 규칙](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>공용 IP 주소 만들기

쿼리(엔진) 공용 IP 주소를 만들려면 다음을 수행하십시오.

1. 포털의 왼쪽 위 모서리에 있는 **+리소스** 만들기 단추를 선택합니다.
1. 네트워크 *보안 그룹을 검색합니다.*
1. **공용 IP 주소**에서 화면 하단에서 **만들기를**선택합니다.
1. 공용 **IP 주소 만들기** 창에서 다음 정보를 완료합니다.
   
   ![공용 IP 양식 만들기](media/vnet-create-cluster-portal/public-ip-blade.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | IP 버전 | IPv4 | IP 버전을 선택합니다. IPv4만 지원합니다.|
    | SKU | Standard | 쿼리(엔진) URI 끝점에 대한 **표준이** 필요합니다. |
    | 이름 | 엔진 핍 | 리소스 그룹에서 공용 IP 주소를 식별하는 이름을 선택합니다.
    | Subscription | 사용자의 구독 | 공용 IP에 사용할 Azure 구독을 선택합니다.|
    | Resource group | 리소스 그룹 | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 서부* | 요구에 가장 적합한 지역을 선택합니다.
    | | | |

1. 공용 IP 주소를 만들려면 **만들기를** 선택합니다.

1. 수집(데이터 관리) 공용 IP 주소를 만들려면 동일한 지침을 따르고 
    * **스쿠**: 기본
    * **IP 주소 할당**: 정적

## <a name="create-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

가상 네트워크 및 서브넷을 만들려면 다음을 수행하십시오.

1. 포털의 왼쪽 위 모서리에 있는 **+리소스** 만들기 단추를 선택합니다.
1. 가상 *네트워크를*검색합니다.
1. **가상 네트워크**화면 하단에서 을 **선택합니다.**
1. 가상 **네트워크 만들기** 창에서 다음 정보를 완료합니다.

   ![가상 네트워크 양식 만들기](media/vnet-create-cluster-portal/vnet-blade.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 클러스터에 사용할 Azure 구독을 선택합니다.|
    | Resource group | 리소스 그룹 | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 이름 | AzureData익탐색기 | 리소스 그룹에서 가상 네트워크를 식별하는 이름을 선택합니다.
    | 지역 | *미국 서부* | 요구에 가장 적합한 지역을 선택합니다.
    | | | |

    > [!NOTE]
    > 프로덕션 워크로드의 경우 [VNet의 계획 서브넷 크기에 따라 서브넷 크기를 계획합니다.](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. **검토 + 만들기**를 선택하여 클러스터 세부 정보를 검토하고 **만들기**를 선택하여 클러스터를 프로비저닝합니다.

1. 배포가 완료되면 **리소스로 이동**을 선택합니다.
1. 서브넷 블레이드로 이동하여 **기본** 서브넷을 **선택합니다.**
    
    ![서브넷 블레이드](media/vnet-create-cluster-portal/subnets.png)

1. **기본** 서브넷 창에서 다음을 수행합니다.
    1. 드롭다운 메뉴에서 **네트워크 보안 그룹을** 선택합니다. 
    1. 이 경우 **네트워크**보안 그룹 이름을 선택합니다. 
    1. **저장**

    ![서브넷 구성](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>클러스터 만들기

클러스터 만들기에 설명된 대로 Azure 리소스 그룹에 정의된 계산 및 저장소 리소스 집합을 사용하여 Azure Data Explorer [클러스터를 만듭니다.](create-cluster-database-portal.md#create-a-cluster)

1. 클러스터 만들기를 완료하기 전에 **Azure Data Explorer 클러스터 만들기** 창에서 **네트워크** 탭을 선택하여 이전 탭에서 만든 리소스를 사용하여 가상 네트워크 세부 정보를 제공합니다.

   ![클러스터 vnet 양식 만들기](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 네트워킹 리소스에 사용할 Azure 구독을 선택합니다.|
    | Virtual Network | AzureData익탐색기 | 이전 단계에서 만든 가상 네트워크를 선택합니다.
    | 서브넷 | default | 이전 단계에서 만든 서브넷을 선택합니다.
    | 공용 IP 쿼리 | 엔진 핍 | 이전 단계에서 만든 쿼리 공용 IP를 선택합니다.
    | 데이터 수집 공용 IP | dm 핍 | 이전 단계에서 만든 공용 IP를 선택합니다.
    | | | |

1. **검토 + 만들기를** 선택하여 클러스터를 만듭니다.
1. 배포가 완료되면 **리소스로 이동**을 선택합니다.

Azure Data Explorer 클러스터를 가상 네트워크에 배포하려면 [Azure 데이터 탐색기 배포 클러스터를 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 리소스 관리자 템플릿에 사용합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 데이터 탐색기를 가상 네트워크에 배포](vnet-create-cluster-portal.md)