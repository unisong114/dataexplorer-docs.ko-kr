---
title: 가상 네트워크에서 Azure Data Explorer 클러스터의 액세스, 수집 및 운영 문제 해결
description: 가상 네트워크에서 Azure Data Explorer 클러스터의 연결, 수집, 클러스터 생성 및 운영 문제 해결
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500763"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>가상 네트워크에서 Azure Data Explorer 클러스터의 액세스, 수집 및 운영 문제 해결

이 섹션에서는 [가상 네트워크에](/azure/virtual-network/virtual-networks-overview)배포된 클러스터에 대한 연결, 운영 및 클러스터 생성 문제를 해결하는 방법을 알아봅니다.

## <a name="access-issues"></a>액세스 문제

공용(cluster.region.kusto.windows.net) 또는 개인(private-cluster.region.kusto.windows.net) 끝점을 사용하여 클러스터에 액세스하는 동안 문제가 있고 가상 네트워크 설정과 관련이 있는 것으로 의심되는 경우 다음 단계를 수행하여 문제를 해결합니다.

### <a name="check-tcp-connectivity"></a>TCP 연결 확인

첫 번째 단계는 Windows 또는 Linux OS를 사용하여 TCP 연결을 확인하는 것입니다.

# <a name="windows"></a>[Windows](#tab/windows)

   1. 클러스터에 연결하는 컴퓨터에 [TCping을](https://www.elifulkerson.com/projects/tcping.php) 다운로드합니다.
   2. 다음 명령을 사용하여 원본 컴퓨터에서 대상을 ping합니다.

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. 클러스터에 연결하는 컴퓨터에 *netcat* 설치

    ```bash
    $ apt-get install netcat
     ```

   2. 다음 명령을 사용하여 원본 컴퓨터에서 대상을 ping합니다.

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

테스트가 성공하지 못하면 다음 단계를 진행합니다. 테스트가 성공하면 TCP 연결 문제로 인한 문제가 아닙니다. 운영 [문제로](#cluster-creation-and-operations-issues) 이동하여 문제를 추가로 해결하십시오.

### <a name="check-the-network-security-group-nsg"></a>NSG(네트워크 보안 그룹) 확인

   클러스터의 서브넷에 연결된 [NSG(네트워크 보안 그룹)에](/azure/virtual-network/security-overview) 포트 443에 대한 클라이언트 컴퓨터의 IP에서 액세스할 수 있는 인바운드 규칙이 있는지 확인합니다.

### <a name="check-route-table"></a>경로 표 확인

   클러스터의 서브넷에 방화벽에 대한 강제 터널링 설정(기본 경로 '0.0.0.0/0'이 포함된 [경로 테이블이](/azure/virtual-network/virtual-networks-udr-overview) 있는 서브넷)이 있는 경우 컴퓨터 IP 주소에 [다음 홉 유형이](/azure/virtual-network/virtual-networks-udr-overview) 있는 경로가 가상 네트워크/인터넷에 있는지 확인합니다. 이 경로는 비대칭 경로 문제를 방지하는 데 필요합니다.

## <a name="ingestion-issues"></a>섭취 문제

인슈어레이션 문제가 발생하고 가상 네트워크 설정과 관련이 있는 것으로 의심되는 경우 다음 단계를 수행합니다.

### <a name="check-ingestion-health"></a>섭취 상태 확인

[클러스터 통합 메트릭이](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) 정상 상태를 나타내는지 확인합니다.

### <a name="check-security-rules-on-data-source-resources"></a>데이터 원본 리소스에 대한 보안 규칙 확인

메트릭에 데이터 원본(이벤트/IoT Hubs에 대한*이벤트 처리* 메트릭)에서 처리된 이벤트가 없음을 나타내는 경우 데이터 원본 리소스(이벤트 허브 또는 저장소)가 방화벽 규칙 또는 서비스 끝점에서 클러스터의 서브넷에서 액세스를 허용하는지 확인합니다.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>클러스터의 서브넷에 구성된 보안 규칙 확인

클러스터의 서브넷에 NSG, UDR 및 방화벽 규칙이 제대로 구성되었는지 확인합니다. 또한 모든 종속 끝점에 대한 네트워크 연결을 테스트합니다. 

## <a name="cluster-creation-and-operations-issues"></a>클러스터 생성 및 운영 문제

클러스터 생성 또는 운영 문제가 발생하고 가상 네트워크 설정과 관련이 있는 것으로 의심되는 경우 다음 단계를 수행하여 문제를 해결합니다.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>REST API로 가상 네트워크 진단

[ARMClient는](https://chocolatey.org/packages/ARMClient) PowerShell을 사용하여 REST API를 호출하는 데 사용됩니다. 

1. ARMClient에 로그인

   ```powerShell
   armclient login
   ```

1. 호출 진단 작업

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 응답 확인

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 작업 완료 대기

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   *상태* 속성이 *완료됨이*표시될 때까지 기다렸다가 *속성* 필드에 다음이 표시됩니다.

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

*Findings* 속성에 빈 결과가 표시되면 모든 네트워크 테스트가 통과되고 연결이 끊어지지 않습니다. 다음 오류가 표시되면 *아웃바운드 종속성 '{dependencyName}:{port}'가 충족되지 않을 수 있습니다(아웃바운드)* 클러스터가 종속 서비스 끝점에 도달할 수 없습니다. 다음 단계를 계속진행합니다.

### <a name="check-network-security-group-nsg"></a>네트워크 보안 그룹(NSG) 확인

[VNet 배포에 대한 종속성의](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment) 지침에 따라 [네트워크 보안 그룹이](/azure/virtual-network/security-overview) 제대로 구성되었는지 확인합니다.

### <a name="check-route-table"></a>경로 표 확인

클러스터의 서브넷에 방화벽(기본 경로 '0.0.0.0/0'이 포함된 [경로 테이블이](/azure/virtual-network/virtual-networks-udr-overview) 있는 서브넷)과 상태 모니터링 IP [주소에](vnet-deployment.md#azure-data-explorer-management-ip-addresses)다음 [health monitoring IP addresses](vnet-deployment.md#health-monitoring-addresses) 홉 [유형](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *인터넷이*있는 경로와 소스 [주소 접두사가](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) *'관리-IP/32'와* *'상태 모니터링-ip/32'로*설정되어 있는 경우. 이 경로는 비대칭 경로 문제를 방지하는 데 필요합니다.

### <a name="check-firewall-rules"></a>방화벽 규칙 확인

터널 서브바운드 트래픽을 방화벽으로 강제 하는 경우 방화벽으로 [아웃바운드 트래픽을 보호하는](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)데 설명된 대로 모든 종속성 FQDN(예: *.blob.core.windows.net)이*방화벽 구성에 허용되는지 확인합니다.
