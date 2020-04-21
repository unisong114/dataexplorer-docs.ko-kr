---
title: Azure 데이터 탐색기를 가상 네트워크에 배포
description: Azure 데이터 탐색기를 가상 네트워크에 배포하는 방법 알아보기
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 170db0d00f42209a2fd55c4009ab2f11eff55641
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610212"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Azure 데이터 탐색기 클러스터를 가상 네트워크에 배포

이 문서에서는 Azure 데이터 탐색기 클러스터를 사용자 지정 Azure 가상 네트워크에 배포할 때 존재하는 리소스에 대해 설명합니다. 이 정보는 가상 네트워크(VNet)의 서브넷에 클러스터를 배포하는 데 도움이 됩니다. Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크란 무엇입니까?](/azure/virtual-network/virtual-networks-overview)

   ![vnet 다이어그램](media/vnet-deployment/vnet-diagram.png)

Azure 데이터 탐색기는 가상 네트워크(VNet)의 서브넷에 클러스터배포를 지원합니다. 이 기능을 사용하면 다음을 수행할 수 있습니다.

* Azure 데이터 탐색기 클러스터 트래픽에 [NSG(네트워크 보안 그룹)](/azure/virtual-network/security-overview) 규칙을 적용합니다.
* 온-프레미스 네트워크를 Azure 데이터 탐색기 클러스터의 서브넷에 연결합니다.
* [서비스 끝점으로](/azure/virtual-network/virtual-network-service-endpoints-overview)데이터 연결 소스(이벤트[허브](/azure/event-hubs/event-hubs-about) 및 [이벤트 그리드)를](/azure/event-grid/overview)보호합니다.

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>VNet에서 Azure 데이터 탐색기 클러스터에 액세스

각 서비스(엔진 및 데이터 관리 서비스)에 대해 다음 IP 주소를 사용하여 Azure Data Explorer 클러스터에 액세스할 수 있습니다.

* **개인 IP**: VNet 내부의 클러스터에 액세스하는 데 사용됩니다.
* **공용 IP**: 관리 및 모니터링을 위해 VNet 외부에서 클러스터에 액세스하고 클러스터에서 시작된 아웃바운드 연결의 소스 주소로 사용됩니다.

서비스에 액세스하기 위해 다음 DNS 레코드가 만들어집니다. 

* `[clustername].[geo-region].kusto.windows.net`(엔진) `ingest-[clustername].[geo-region].kusto.windows.net` (데이터 관리)는 각 서비스에 대한 공용 IP에 매핑됩니다. 

* `private-[clustername].[geo-region].kusto.windows.net`(엔진) `private-ingest-[clustername].[geo-region].kusto.windows.net` (데이터 관리)는 각 서비스에 대한 개인 IP에 매핑됩니다.

## <a name="plan-subnet-size-in-your-vnet"></a>VNet에서 서브넷 크기 계획

Azure 데이터 탐색기 클러스터를 호스트하는 데 사용되는 서브넷의 크기는 서브넷을 배포한 후 변경할 수 없습니다. VNet에서 Azure Data Explorer는 각 VM에 대해 하나의 개인 IP 주소와 내부 부하 분산기(엔진 및 데이터 관리)에 대해 두 개의 개인 IP 주소를 사용합니다. 또한 Azure 네트워킹은 각 서브넷에 대해 5개의 IP 주소를 사용합니다. Azure 데이터 탐색기는 데이터 관리 서비스에 대해 두 개의 VM을 프로비전합니다. 엔진 서비스 VM은 사용자 구성 스케일 용량에 따라 프로비저닝됩니다.

총 IP 주소 수:

| 사용 | 주소 수 |
| --- | --- |
| 엔진 서비스 | 인스턴스당 1개 |
| 데이터 관리 서비스 | 2 |
| 내부 부하 분산 장치 | 2 |
| Azure 예약 주소 | 5 |
| **총** | **#engine_instances + 9** |

> [!IMPORTANT]
> Azure Data Explorer를 배포한 후에는 변경할 수 없으므로 서브넷 크기를 미리 계획해야 합니다. 따라서 그에 따라 필요한 서브넷 크기를 예약해야 합니다.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Azure 데이터 탐색기에 연결하기 위한 서비스 끝점

[Azure 서비스 엔드포인트를](/azure/virtual-network/virtual-network-service-endpoints-overview) 사용하면 Azure 다중 테넌트 리소스를 가상 네트워크에 보호할 수 있습니다.
서브넷에 Azure Data Explorer 클러스터를 배포하면 Azure Data Explorer 서브넷의 기본 리소스를 제한하면서 [이벤트 허브](/azure/event-hubs/event-hubs-about) 또는 [이벤트 그리드를](/azure/event-grid/overview) 사용하여 데이터 연결을 설정할 수 있습니다.

> [!NOTE]
> [저장소](/azure/storage/common/storage-introduction) 및 [이벤트 허브]를 사용하여 EventGrid 설정을 사용하는 경우 구독에 사용된 저장소 계정은 [방화벽 구성에서](/azure/storage/common/storage-network-security)신뢰할 수 있는 Azure 플랫폼 서비스를 허용하면서 Azure Data Explorer의 서브넷에 대한 서비스 끝점으로 잠글 수 있지만 이벤트 허브는 신뢰할 수 있는 [Azure 플랫폼 서비스를](/azure/event-hubs/event-hubs-service-endpoints)지원하지 않으므로 서비스 끝점을 사용할 수 없습니다.

## <a name="dependencies-for-vnet-deployment"></a>VNet 배포에 대한 종속성

### <a name="network-security-groups-configuration"></a>네트워크 보안 그룹 구성

[NSG(네트워크 보안 그룹)는](/azure/virtual-network/security-overview) VNet 내에서 네트워크 액세스를 제어하는 기능을 제공합니다. Azure 데이터 탐색기는 HTTP(443)와 TDS(1433)의 두 개의 끝점을 사용하여 액세스할 수 있습니다. 클러스터의 관리, 모니터링 및 적절한 운영을 위해 이러한 끝점에 액세스할 수 있도록 다음 NSG 규칙을 구성해야 합니다.

#### <a name="inbound-nsg-configuration"></a>인바운드 NSG 구성

| **사용**   | **보낸 사람**   | **대상**   | **프로토콜**   |
| --- | --- | --- | --- |
| 관리  |[ADX 관리 주소](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(서비스태그) | ADX 서브넷:443  | TCP  |
| 상태 모니터링  | [ADX 상태 모니터링 주소](#health-monitoring-addresses)  | ADX 서브넷:443  | TCP  |
| ADX 내부 통신  | ADX 서브넷: 모든 포트  | ADX 서브넷:모든 포트  | 모두  |
| Azure 로드 밸러블러 인바운드 허용(상태 프로브)  | AzureLoadBalancer  | ADX 서브넷:80,443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>아웃바운드 NSG 구성

| **사용**   | **보낸 사람**   | **대상**   | **프로토콜**   |
| --- | --- | --- | --- |
| Azure Storage에 대한 종속성  | ADX 서브넷  | 저장 공간:443  | TCP  |
| Azure 데이터 레이크에 대한 종속성  | ADX 서브넷  | AzureDataLake:443  | TCP  |
| EventHub 인비전에 및 서비스 모니터링  | ADX 서브넷  | 이벤트허브:443,5671  | TCP  |
| 지표 게시  | ADX 서브넷  | AzureMonitor:443 | TCP  |
| Azure 모니터 구성 다운로드  | ADX 서브넷  | [Azure 모니터 구성 끝점 주소](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| 활성 디렉토리(해당하는 경우) | ADX 서브넷 | AzureActive 디렉터리:443 | TCP |
| 인증 기관 | ADX 서브넷 | 인터넷:80 | TCP |
| 내부 통신  | ADX 서브넷  | ADX 서브넷: 모든 포트  | 모두  |
| 용 `sql\_request` 으로 사용되는 `http\_request` 포트 및 플러그인  | ADX 서브넷  | 인터넷:사용자 지정  | TCP  |

### <a name="relevant-ip-addresses"></a>관련 IP 주소

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 데이터 탐색기 관리 IP 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 20.37.26.134 |
| 오스트레일리아 센트럴2 | 20.39.99.177 |
| 오스트레일리아 동부 | 40.82.217.84 |
| 오스트레일리아 남동부 | 20.40.161.39 |
| 브라질남부 | 191.233.25.183 |
| 캐나다 중부 | 40.82.188.208 |
| 캐나다 동부 | 40.80.255.12 |
| 인도 중부 | 40.81.249.251, 104.211.98.159 |
| 미국 중부 | 40.67.188.68 |
| 미국 중부 EUAP | 40.89.56.69 |
| 동아시아 | 20.189.74.103 |
| 미국 동부 | 52.224.146.56 |
| 미국 동부2 | 52.232.230.201 |
| 이스트 US2 EUAP | 52.253.226.110 |
| 프랑스 중부 | 40.66.57.91 |
| 프랑스 남부 | 40.82.236.24 |
| 일본 동부 | 20.43.89.90 |
| 일본 서부 | 40.81.184.86 |
| 한국 중부 | 40.82.156.149 |
| 한국 남부 | 40.80.234.9 |
| 미국 중북부 | 40.81.45.254 |
| 북유럽 | 52.142.91.221 |
| 남아프리카 북부 | 102.133.129.138 |
| 남아프리카 공화국 서부 | 102.133.0.97 |
| 미국 중남부 | 20.45.3.60 |
| 동남아시아 | 40.119.203.252 |
| 인도 남부 | 40.81.72.110, 104.211.224.189 |
| 영국 남부 | 40.81.154.254 |
| 영국 서부 | 40.81.122.39 |
| 미국 중서부 | 52.159.55.120 |
| 서유럽 | 51.145.176.215 |
| 인도 서부 | 40.81.88.112, 104.211.160.120 |
| 미국 서부 | 13.64.38.225 |
| 미국 서부2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>상태 모니터링 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 191.239.64.128 |
| 오스트레일리아 중부 2 | 191.239.64.128 |
| 오스트레일리아 동부 | 191.239.64.128 |
| 오스트레일리아 남동부 | 191.239.160.47 |
| 브라질 남부 | 23.98.145.105 |
| 캐나다 중부 | 168.61.212.201 |
| 캐나다 동부 | 168.61.212.201 |
| 인도 중부 | 23.99.5.162 |
| 미국 중부 | 168.61.212.201 |
| 미국 중부 EUAP | 168.61.212.201 |
| 동아시아 | 168.63.212.33 |
| 미국 동부 | 137.116.81.189 |
| 미국 동부 2 | 137.116.81.189 |
| 미국 동부 2 EUAP | 137.116.81.189 |
| 프랑스 중부 | 23.97.212.5 |
| 프랑스 남부 | 23.97.212.5 |
| 일본 동부 | 138.91.19.129 |
| 일본 서부 | 138.91.19.129 |
| 한국 중부 | 138.91.19.129 |
| 한국 남부 | 138.91.19.129 |
| 미국 중북부 | 23.96.212.108 |
| 북유럽 | 191.235.212.69 
| 남아프리카 북부 | 104.211.224.189 |
| 남아프리카 공화국 서부 | 104.211.224.189 |
| 미국 중남부 | 23.98.145.105 |
| 인도 남부 | 23.99.5.162 |
| 동남아시아 | 168.63.173.234 |
| 영국 남부 | 23.97.212.5 |
| 영국 서부 | 23.97.212.5 |
| 미국 중서부 | 168.61.212.201 |
| 서유럽 | 23.97.212.5 |
| 인도 서부 | 23.99.5.162 |
| 미국 서부 | 23.99.5.162 |
| 미국 서부 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure 모니터 구성 끝점 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 52.148.86.165 |
| 오스트레일리아 중부 2 | 52.148.86.165 |
| 오스트레일리아 동부 | 52.148.86.165 |
| 오스트레일리아 남동부 | 52.148.86.165 |
| 브라질 남부 | 13.68.89.19 |
| 캐나다 중부 | 13.90.43.231 |
| 캐나다 동부 | 13.90.43.231 |
| 인도 중부 | 13.71.25.187 |
| 미국 중부 | 52.173.95.68 |
| 미국 중부 EUAP | 13.90.43.231 |
| 동아시아 | 13.75.117.221 |
| 미국 동부 | 13.90.43.231 |
| 미국 동부 2 | 13.68.89.19 |    
| 미국 동부 2 EUAP | 13.68.89.19 |
| 프랑스 중부 | 52.174.4.112 |
| 프랑스 남부 | 52.174.4.112 |
| 일본 동부 | 13.75.117.221 |
| 일본 서부 | 13.75.117.221 |
| 한국 중부 | 13.75.117.221 |
| 한국 남부 | 13.75.117.221 |
| 미국 중북부 | 52.162.240.236 |
| 북유럽 | 52.169.237.246 |
| 남아프리카 북부 | 13.71.25.187 |
| 남아프리카 공화국 서부 | 13.71.25.187 |
| 미국 중남부 | 13.84.173.99 |
| 인도 남부 | 13.71.25.187 |
| 동남아시아 | 52.148.86.165 |
| 영국 남부 | 52.174.4.112 |
| 영국 서부 | 52.169.237.246 |
| 미국 중서부 | 52.161.31.69 |
| 서유럽 | 52.174.4.112 |
| 인도 서부 | 13.71.25.187 |
| 미국 서부 | 40.78.70.148 |
| 미국 서부 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>익스프레스루트 설정

ExpressRoute를 사용하여 온프레미스 네트워크에 Azure 가상 네트워크에 연결합니다. 일반적인 설정은 BGP(경계 게이트웨이 프로토콜) 세션을 통해 기본 경로(0.0.0.0/0)를 광고하는 것입니다. 이렇게 하면 가상 네트워크에서 나오는 트래픽이 트래픽을 삭제할 수 있는 고객의 전제 네트워크로 전달되어 아웃바운드 흐름이 중단됩니다. 이 기본값을 극복하기 위해 [UDR(사용자 정의](/azure/virtual-network/virtual-networks-udr-overview#user-defined) 경로)(0.0.0.0/0)을 구성할 수 있으며 다음 홉은 *인터넷이*됩니다. UDR이 BGP보다 우선하므로 트래픽은 인터넷으로 향합니다.

## <a name="securing-outbound-traffic-with-firewall"></a>방화벽으로 아웃바운드 트래픽 보호

[Azure 방화벽](/azure/firewall/overview) 또는 가상 어플라이언스를 사용하여 도메인 이름을 제한하려는 아웃바운드 트래픽을 보호하려면 방화벽에서 다음 정규화된 도메인 이름(FQDN)을 허용해야 합니다.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
*.identity.azure.net:443
*.vault.azure.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

또한 비대칭 경로 문제를 방지하기 위해 다음 홉 *인터넷을* 사용하여 [관리 주소](#azure-data-explorer-management-ip-addresses) 및 상태 모니터링 주소로 [서브넷의](#health-monitoring-addresses) [경로 테이블을](/azure/virtual-network/virtual-networks-udr-overview) 정의해야 합니다.

예를 들어 **미국 서부** 지역의 경우 다음 DR을 정의해야 합니다.

| 이름 | 주소 접두사 | 다음 홉 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | 인터넷 |
| ADX_Monitoring | 23.99.5.162/32 | 인터넷 |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기 클러스터를 VNet에 배포

Azure Data Explorer 클러스터를 가상 네트워크에 배포하려면 [Azure 데이터 탐색기 배포 클러스터를 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 리소스 관리자 템플릿에 사용합니다.

이 템플릿은 클러스터, 가상 네트워크, 서브넷, 네트워크 보안 그룹 및 공용 IP 주소를 만듭니다.
