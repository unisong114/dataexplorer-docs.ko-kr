---
title: 가상 네트워크에서 Azure 데이터 탐색기 클러스터의 액세스, 수집 및 작업 문제 해결
description: 가상 네트워크에서 Azure 데이터 탐색기 클러스터의 연결, 수집, 클러스터 생성 및 작업 문제 해결
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/24/2020
ms.openlocfilehash: 498912016c595646e1ef4b4df9bec427c262b6e9
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873885"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>가상 네트워크에서 Azure 데이터 탐색기 클러스터의 액세스, 수집 및 작업 문제 해결

이 섹션에서는 [Virtual Network](/azure/virtual-network/virtual-networks-overview)에 배포 되는 클러스터에 대 한 연결, 운영 및 클러스터 생성 문제를 해결 하는 방법에 대해 알아봅니다.

## <a name="access-issues"></a>액세스 문제

공용 (cluster.region.kusto.windows.net) 또는 개인 (private-cluster.region.kusto.windows.net) 끝점을 사용 하 여 클러스터에 액세스 하는 동안 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계를 수행 하 여 문제를 해결 하십시오.

### <a name="check-tcp-connectivity"></a>TCP 연결 확인

첫 번째 단계는 Windows 또는 Linux OS를 사용 하 여 TCP 연결을 확인 하는 것입니다.

# <a name="windows"></a>[Windows](#tab/windows)

1. 클러스터에 연결 하는 컴퓨터에 [Tcping](https://www.elifulkerson.com/projects/tcping.php) 을 다운로드 합니다.
1. 다음 명령을 사용 하 여 원본 컴퓨터에서 대상에 대해 Ping을 수행 합니다.

   ```cmd
   C:\> tcping -t yourcluster.kusto.windows.net 443 
   ** Pinging continuously.  Press control-c to stop **
   Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
   ```

# <a name="linux"></a>[Linux](#tab/linux)

1. 클러스터에 연결 하는 컴퓨터에 *netcat* 설치

   ```bash
   $ apt-get install netcat
   ```

1. 다음 명령을 사용 하 여 원본 컴퓨터에서 대상에 대해 Ping을 수행 합니다.

   ```bash
   $ netcat -z -v yourcluster.kusto.windows.net 443
   Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
   ```
---

테스트에 실패 하면 다음 단계를 진행 합니다. 테스트에 성공 하면 TCP 연결 문제로 인해 문제가 발생 하지 않습니다. 문제 해결을 위해 [운영 문제로](#cluster-creation-and-operations-issues) 이동 합니다.

### <a name="check-the-network-security-group-nsg"></a>NSG (네트워크 보안 그룹)를 확인 합니다.

클러스터의 서브넷에 연결 된 nsg ( [네트워크 보안 그룹](/azure/virtual-network/security-overview) )에 클라이언트 컴퓨터의 IP에서 포트 443에 대 한 액세스를 허용 하는 인바운드 규칙이 있는지 확인 합니다.

### <a name="check-route-table"></a>경로 테이블 확인

클러스터의 서브넷에서 방화벽 (기본 경로 ' 0.0.0.0/0 '을 포함 하는 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview) 을 포함 하는 서브넷)에 강제 터널링을 설정 하는 경우 컴퓨터 IP 주소에 [다음 홉 유형이](/azure/virtual-network/virtual-networks-udr-overview) VirtualNetwork/Internet 인 경로가 있는지 확인 합니다. 이 경로는 비대칭 경로 문제를 방지 하는 데 필요 합니다.

## <a name="ingestion-issues"></a>수집 문제

수집 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계를 수행 합니다.

### <a name="check-ingestion-health"></a>수집 상태 확인

[클러스터 수집 메트릭이](using-metrics.md#ingestion-health-and-performance-metrics) 정상 상태 인지 확인 합니다.

### <a name="check-security-rules-on-data-source-resources"></a>데이터 원본 리소스에 대 한 보안 규칙 확인

메트릭이 데이터 원본에서 처리 된 이벤트 (이벤트/IoT 허브에 대 한 이벤트*처리* 된 메트릭)를 표시 하지 않는 경우 데이터 원본 리소스 (이벤트 허브 또는 저장소)가 방화벽 규칙이 나 서비스 끝점의 클러스터 서브넷에서 액세스를 허용 하는지 확인 합니다.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>클러스터의 서브넷에 구성 된 보안 규칙 확인

클러스터의 서브넷에 NSG, UDR 및 방화벽 규칙이 적절히 구성 되어 있는지 확인 합니다. 또한 모든 종속 끝점에 대 한 네트워크 연결을 테스트 합니다. 

## <a name="cluster-creation-and-operations-issues"></a>클러스터 만들기 및 작업 문제

클러스터 만들기 또는 작업 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계에 따라 문제를 해결 하십시오.

### <a name="check-the-dns-servers-configuration"></a>"DNS 서버" 구성 확인

사용자 지정 DNS 서버는 지원 되지 않습니다. Virtual Network의 **DNS 서버** 구성 섹션에서 기본 옵션을 사용 합니다.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>REST API를 사용 하 여 가상 네트워크 진단

[ARMClient](https://chocolatey.org/packages/ARMClient) 는 PowerShell을 사용 하 여 REST API를 호출 하는 데 사용 됩니다. 

1. ARMClient에 로그인

   ```powerShell
   armclient login
   ```

1. 진단 작업 호출

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
    
   *Status* 속성이 *완료*로 표시 될 때까지 기다렸다가 *속성* 필드에 다음이 표시 되어야 합니다.

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

검색 결과 *속성이 빈* 결과를 표시 하는 경우 모든 네트워크 테스트가 통과 되 고 연결이 끊어지지 않았음을 의미 합니다. 다음 오류가 표시 되 면 *아웃 바운드 종속성 ' {dependencyName}: {port} '이 (가) 충족 되지 않을 수 있습니다 (아웃 바운드)*. 클러스터가 종속 서비스 끝점에 연결할 수 없습니다. 다음 단계를 진행 합니다.

### <a name="check-network-security-group-nsg"></a>NSG (네트워크 보안 그룹) 확인

[VNet 배포에 대 한 종속성](vnet-deployment.md#dependencies-for-vnet-deployment) 의 지침에 따라 [네트워크 보안 그룹이](/azure/virtual-network/security-overview) 제대로 구성 되어 있는지 확인 합니다.

### <a name="check-route-table"></a>경로 테이블 확인

클러스터의 서브넷에 강제 터널링이 방화벽으로 설정 되어 있는 경우 (기본 경로 ' 0.0.0.0/0 '을 포함 하는 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview) 을 포함 하는 서브넷) [관리 ip 주소](vnet-deployment.md#azure-data-explorer-management-ip-addresses)및 [상태 모니터링 IP 주소](vnet-deployment.md#health-monitoring-addresses) 에 [다음 홉 유형](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *인터넷*이 있는 경로가 있는지 확인 하 고 [, 원본 주소 접두사](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) 를 *' 관리 ip/32 '* 및 *' 상태-모니터링-i p s '* 로 설정 합니다. 이 경로는 비대칭 경로 문제를 방지 하는 데 필요 합니다.

### <a name="check-firewall-rules"></a>방화벽 규칙 확인

방화벽에 대 한 터널 서브넷 아웃 바운드 트래픽을 강제 적용 하는 경우 방화벽을 [사용 하 여 아웃 바운드 트래픽 보안](vnet-deployment.md#securing-outbound-traffic-with-firewall)에 설명 된 대로 방화벽 구성에서 모든 종속성 FQDN (예: *blob.core.windows.net*)이 허용 되는지 확인 합니다.
