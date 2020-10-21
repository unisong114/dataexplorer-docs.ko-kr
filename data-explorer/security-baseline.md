---
title: 데이터 탐색기에 대 한 Azure 보안 기준
description: 데이터 탐색기에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 620755a179bfb434b1532d453c0ab5283ec0bfaa
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343490"
---
# <a name="azure-security-baseline-for-data-explorer"></a>데이터 탐색기에 대 한 Azure 보안 기준

데이터 탐색기에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 그룹: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: Azure 데이터 탐색기는 가상 네트워크의 서브넷에 클러스터를 배포 하는 것을 지원 합니다. 이 기능을 사용 하면 Azure 데이터 탐색기 클러스터 트래픽에 NSG (네트워크 보안 그룹) 규칙을 적용 하 고, 온-프레미스 네트워크를 Azure 데이터 탐색기 클러스터의 서브넷에 연결 하 고, 서비스 엔드포인트를 사용 하 여 데이터 연결 원본 (이벤트 허브 및 Event Grid)을 보호할 수 있습니다.

가상 네트워크에 Azure 데이터 탐색기 클러스터를 배포 하는 방법:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NICS의 구성 및 트래픽을 모니터링 및 기록

**지침**: nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법:  https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center에서 제공 하는 네트워크 보안 이해:  https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

**지침**: 해당 사항 없음 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 데이터 탐색기 클러스터를 보호 하는 가상 네트워크에서 Azure DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

DDoS 보호를 구성 하는 방법:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center 통합 위협 인텔리전스 이해:  https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure 데이터 탐색기 클러스터를 보호 하는 데 사용 되는 nsg (네트워크 보안 그룹)에서 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법:  https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 배포

**지침**: 해당 사항 없음 이 컨트롤은 끝점 또는 방화벽에서 수행 됩니다.


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Virtual Network 서비스 태그를 사용 하 여 azure 데이터 탐색기 클러스터와 연결 된 네트워크 보안 그룹 또는 azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

서비스 태그 이해 및 사용:  https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Azure 데이터 탐색기에 대 한 서비스 태그 구성 요구 사항:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

**지침**: 고객은 Azure Policy를 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

또한 고객은 Azure 청사진을 사용 하 여 단일 청사진 정의에서 ARM 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint를 만드는 방법:  https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 데이터 탐색기 클러스터의 네트워크 보안 및 트래픽 흐름과 관련 된 nsg (네트워크 보안 그룹) 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 만들고 사용 하는 방법:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 구성을 확인 (및/또는 재구성) 합니다.

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인 된 시간 동기화 원본을 사용 하십시오.

**지침**: Azure 리소스에 대 한 시간 원본을 Microsoft에서 유지 관리 하므로 고객은 고객이 소유한 계산 배포에 대 한 시간 동기화를 업데이트할 수 있습니다.

Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure 데이터 탐색기는 수집 성공 및 실패에 대 한 정보를 제공 하기 위해 진단 로그를 사용 합니다. 작업 로그를 Azure Storage, 이벤트 허브 또는 Log Analytics로 내보내 수집 상태를 모니터링할 수 있습니다.

Azure 데이터 탐색기 수집 작업을 모니터링 하는 방법:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure 데이터 탐색기에서 모니터링 데이터를 수집 하 고 쿼리 하는 방법:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

**지침**: 서비스 특정 작업 및 로깅에 대 한 액세스 및 로깅을 위해 Azure 데이터 탐색기에 대 한 진단 설정을 사용 하도록 설정 합니다. 리소스에 대 한 개략적인 로깅을 포함 하는 Azure Monitor 내의 Azure 활동 로그는 기본적으로 사용 하도록 설정 되어 있습니다.

Azure 데이터 탐색기 수집 작업을 모니터링 하는 방법:  https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법:  https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure platform logs 개요:  https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure 데이터 탐색기에 대 한 진단 설정을 사용 하도록 설정한 후 Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대해 쿼리를 수행 합니다.

Log Analytics 작업 영역 이해:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 사용자 지정 쿼리를 수행 하는 방법:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 해당 사항 없음 Azure 데이터 탐색기에는이 기능이 없습니다.


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

**지침**: 해당 사항 없음 Azure 데이터 탐색기는 맬웨어 방지 로깅을 처리 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 없음: DNS 쿼리가 Azure 데이터 탐색기의 함수가 아닙니다.


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 그룹: ID 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure 데이터 탐색기에서 보안 역할은 데이터베이스 또는 테이블과 같은 보안 리소스에 대해 작업할 권한이 있는 보안 주체 (사용자 및 응용 프로그램) 및 허용 되는 작업을 정의 합니다.  Kusto 쿼리를 활용 하 여 Azure 데이터 탐색기 클러스터와 데이터베이스에 대 한 관리자 역할의 원칙을 나열할 수 있습니다.
[Kusto 쿼리를 사용 하 여 Azure 데이터 탐색기의 보안 역할 관리](kusto/management/security-roles.md)



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당 하는 경우 기본 암호를 변경 합니다.

**지침**: Azure AD에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 강제로 만들도록 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 marketplace 서비스를 담당 합니다.


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 전용 관리자 계정을 사용 해야 합니다.

**지침**: 고객은 전용 관리 계정의 사용에 대 한 표준 운영 절차를 만들 수 있습니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 고객은 Microsoft 서비스 및 Azure ARM에 대해 Azure AD Privileged Identity Management 권한 있는 역할을 사용 하 여 Just-in-time/Just-in-time 액세스를 사용 하도록 설정할 수 있습니다. 

Azure AD Privileged Identity Management 이란?: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory를 사용 하 여 SSO (Single Sign-On)를 활용 합니다.

**지침**: 가능한 경우 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신, 고객이 Azure AD (Azure Active Directory)를 사용 하 여 SSO를 사용할 수 있습니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다.

Azure AD를 사용 하 여 SSO 이해:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증을 사용 하십시오.

**지침**: Azure Active Directory (Azure AD) multi-factor AUTHENTICATION (MFA)을 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Azure Security Center 내에서 id 및 액세스를 모니터링 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터 (권한 있는 액세스 워크스테이션) 사용

**지침**: MFA (multi-factor authentication)가 구성 된 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다.

Privileged Access Workstation에 대한 자세한 정보: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 사용 하도록 설정 하는 방법:  https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: 관리자 계정에 대 한 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD (Azure Active Directory) 보안 보고서를 사용 합니다. Azure Security Center를 사용 하 여 id 및 액세스 작업 모니터링

위험한 활동에 대해 플래그가 지정 된 Azure AD 사용자를 확인 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center에서 사용자 id 및 액세스 활동을 모니터링 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 고객은 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

Azure에서 명명 된 위치를 구성 하는 방법: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-utilize-azure-active-directory"></a>3.9: Azure Active Directory 활용

**지침**: azure 데이터 탐색기에 인증 하는 데는 Azure Active Directory (azure AD)가 기본 설정 된 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.

사용자 인증(대화형 로그온): 사용자 주체를 인증하는 데 사용됩니다.

애플리케이션 인증(비대화형 로그온): 사용자가 없는 상태에서 실행/인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

[Azure 데이터 탐색기 Access Control 개요](kusto/management/access-control/index.md)

[Azure Active Directory 인증](kusto/management/access-control/aad.md)



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스를 검토 하 고 조정 합니다.

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

[Azure 데이터 탐색기 액세스를 위해 Azure AD를 사용 하 여 인증 하는 방법](kusto/management/access-control/how-to-authenticate-with-aad.md)

[Azure AD 보고](/azure/active-directory/reports-monitoring/)

[Azure ID 액세스 검토를 사용하는 방법](/azure/active-directory/governance/access-reviews-overview)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Siem (보안 정보 및 이벤트 관리)/모니터링 도구와 통합할 수 있도록 하는 모니터링을 위해 Azure AD (Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 사용할 수 있습니다.

 Azure Active Directory 사용자 계정에 대 한 진단 설정을 만들어 감사 로그 및 로그인 로그를 Log Analytics 작업 영역에 전송 하 여이 프로세스를 간소화할 수 있습니다. 사용자가 Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure Monitor에 통합 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory (Azure AD) 위험 검색 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 또한 추가 조사를 위해 데이터를 Azure 센티널로 수집할 수 있습니다.

Azure AD 위험한 로그인을 확인 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법:  https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft에서 고객 데이터에 액세스 해야 하는 지원 시나리오에서 고객이 고객 데이터 액세스 요청을 검토 하 고 승인 또는 거부할 수 있는 인터페이스를 제공 고객 Lockbox 합니다.

고객 Lockbox 이해:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 그룹: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure 데이터 탐색기 클러스터는 가상 네트워크/서브넷에서 다른 리소스와 분리 하 고, 적절 하 게 태그를 지정 하 고, NSG (네트워크 보안 그룹) 또는 Azure 방화벽 내에서 보호 해야 합니다. 중요 한 데이터를 저장 하거나 처리 하는 클러스터 데이터 탐색기 충분히 격리 되어야 합니다.

추가 Azure 구독을 만드는 방법:  https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹를 만드는 방법:  https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용 하는 방법:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

가상 네트워크에 Azure 데이터 탐색기 클러스터를 배포 하는 방법:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment

보안 구성을 사용 하 여 NSG를 만드는 방법:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 차단 합니다.

**지침**: 해당 사항 없음 Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요 한 정보를 암호화 합니다.

**지침**: Azure 데이터 탐색기 클러스터는 기본적으로 TLS 1.2을 협상 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>활성 검색 도구를 사용 하 여 중요 한 데이터 식별</div>

**지침**: Azure 데이터 탐색기에 대 한 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Azure RBAC를 사용하여 리소스에 대한 액세스 제어</div>

**지침**: Azure 데이터 탐색기를 사용 하면 RBAC (역할 기반 액세스 제어) 모델을 사용 하 여 데이터베이스 및 테이블에 대 한 액세스를 제어할 수 있습니다. 이 모델에서 주체(사용자, 그룹 및 앱)는 역할에 매핑됩니다. 주체는 자신에게 할당된 역할에 따라 리소스에 액세스할 수 있습니다.

Azure 데이터 탐색기에 대 한 RBAC를 구성 하는 방법에 대 한 역할 및 사용 권한 및 지침 목록: https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 권장 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft는 Azure 데이터 탐색기에 대 한 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

Azure의 고객 데이터 보호 이해:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

**지침**: Azure Disk Encryption는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. 클러스터 가상 컴퓨터의 OS 및 데이터 디스크에 대 한 볼륨 암호화를 제공 합니다. 또한 디스크 암호화 키 및 암호를 제어 하 고 관리 하는 데 사용할 수 있는 Azure Key Vault와 통합 되며, Azure Storage 중에 VM 디스크의 모든 데이터가 미사용에서 암호화 되도록 보장 합니다.

Azure 데이터 탐색기 클러스터에 대해 미사용 암호화를 사용 하도록 설정 하는 방법:  https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 azure 데이터 탐색기 클러스터에서 리소스 수준 변경이 발생 하는 경우에 대 한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 그룹: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화 된 취약점 검사 도구 실행

**지침**: Azure 데이터 탐색기 리소스를 보호 하는 Azure Security Center의 권장 사항을 따릅니다.

Microsoft는 Azure 데이터 탐색기를 지 원하는 기본 시스템 에서도 취약점 관리를 수행 합니다.

Azure Security Center 권장 사항 이해: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center 모니터링**: 예

**책임**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화 된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화 된 Third Party Software 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백엔드 취약성 검색 비교

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 활용 하 여 검색 된 취약점의 재구성에 우선 순위를 지정 합니다.

**지침**: Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 합니다.
보안 점수 Azure Security Center 이해:  https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 그룹: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Azure Asset Discovery 활용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 확인 하는 방법:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

태그를 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

**지침**: 적절 한 명명 규칙, 태그 지정, 관리 그룹 또는 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적할 수 있습니다. Azure 리소스 그래프를 사용 하 여 정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 할 수 있습니다. 

추가 Azure 구독을 만드는 방법:  https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법: https://docs.microsoft.com/azure/governance/management-groups/create

그룹을 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대해 승인 된 소프트웨어의 인벤토리를 만들어야 합니다.  


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

**지침**: Azure 정책을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

다음을 사용 하 여 정책 생성 이벤트를 모니터링할 수 있습니다. 

Azure Monitor를 사용 하 여 모니터링할 수 있는 활동 로그입니다.

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색할 수 있습니다.

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리를 실행 합니다. https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure Monitor를 사용 하 여 활동 로그 경고를 만들고 확인 하 고 관리 합니다.  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="68-utilize-only-approved-applications"></a>6.8: 승인 된 응용 프로그램만 활용 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="69-utilize-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 활용

**지침**: Azure 정책을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 샘플: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure Resource Manager와 상호 작용하는 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 Azure 구독 내에서 리소스를 만들고 변경할 수 없습니다. 

조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스 관리: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 그룹: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

**지침**: Azure Policy 별칭을 사용 하 여 Azure 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

사용 가능한 Azure Policy 별칭을 보는 방법: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기: https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

보안 권장 사항-참조 가이드: https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: 운영 체제에 대 한 보안 구성을 설정 합니다.

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: 모든 Azure 리소스에 대 한 보안 구성을 유지 합니다.

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.  변경 내용 추적, 정책 준수 대시보드 또는 사용자 지정 솔루션과 같은 솔루션을 사용 하 여 사용자 환경에서 보안 변경 내용을 쉽게 식별할 수 있습니다.

Azure Policy 효과 이해: https://docs.microsoft.com/azure/governance/policy/concepts/effects

규정 준수를 적용 하는 정책 만들기 및 관리:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

변경 내용 추적 솔루션을 사용 하 여 사용자 환경의 변경 내용을 추적 합니다. https://docs.microsoft.com/azure/automation/change-tracking

Azure 리소스의 준수 데이터 가져오기: https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: 운영 체제에 대 한 보안 구성 유지

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

**지침**: Azure Repos을 사용 하 여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, 필요한 상태 구성 스크립트 등과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다.

Azure DevOps에서 코드를 저장 하는 방법:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure DevOps의 사용 권한 및 그룹에 대 한 정보:  https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.  또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다.

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

별칭을 사용 하는 방법: https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure 정책을 사용 하 여 azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [거부] 및 [없는 경우 배포]를 사용 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="711-securely-manage-azure-secrets"></a>7.11: Azure 비밀을 안전 하 게 관리

**지침**: Azure Disk Encryption는 Azure 데이터 탐색기 클러스터 가상 머신의 OS 및 데이터 디스크에 대 한 볼륨 암호화를 제공 합니다. 또한 디스크 암호화 키 및 암호를 제어 하 고 관리 하는 데 사용할 수 있는 Azure Key Vault와 통합 되며, Azure Storage 중에 VM 디스크의 모든 데이터가 미사용 시 암호화 되도록 보장 합니다.

Azure 데이터 탐색기에서 클러스터를 보호 하는 방법:  https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: id를 안전 하 게 자동으로 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다. 관리 Id를 구성 하는 방법:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure 데이터 탐색기 클러스터에 대 한 관리 되는 id를 구성 합니다.  https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

자격 증명 스캐너를 설정하는 방법: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 그룹: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 활용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 azure 서비스 (예: Azure 데이터 탐색기)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 azure 서비스 (예: azure 데이터 탐색기)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

비 계산 Azure 리소스 (예: Azure 데이터 탐색기, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)로 업로드 되는 콘텐츠를 미리 검색 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 그룹: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: 데이터 탐색기 클러스터에서 사용 하는 Microsoft Azure 저장소 계정의 데이터는 항상 내구성 및 고가용성을 보장 하기 위해 복제 됩니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다.

Azure Storage 중복성 및 Service-Level 계약 이해:  https://docs.microsoft.com/azure/storage/common/storage-redundancy

[저장소로 데이터 내보내기](kusto/management/data-export/export-data-to-storage.md)



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure 데이터 탐색기는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대 한 추가 제어를 위해 고객 관리 키를 제공 하 여 데이터 암호화에 사용할 수 있습니다. 고객 관리 키는 Azure Key Vault에 저장 되어야 합니다. 

C #을 사용 하 여 고객이 관리 하는 키 구성: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키를 구성 합니다.  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Azure Key Vault 인증서를 백업 하는 방법:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

**지침**: 정기적으로 Azure Key Vault 암호의 데이터 복원을 테스트 합니다.

Azure Key Vault 인증서를 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

C #을 사용 하 여 고객이 관리 하는 키 구성: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키를 구성 합니다.  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

**지침**: Key Vault에서 Soft-Delete를 사용 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호할 수 있습니다.  삭제 된 상태의 자격 증명 모음 또는 개체가 있는 경우 보존 기간이 경과할 때까지 제거할 수 없도록 보호 제거를 사용 하도록 설정할 수도 있습니다.  

Key Vault에서 Soft-Delete를 사용 하도록 설정 하 고 보호를 제거 하는 방법:  https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

C #을 사용 하 여 고객이 관리 하는 키 구성: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키를 구성 합니다.  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 그룹: 인시던트 대응](/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-incident-response-guide"></a>10.1: 인시던트 응답 만들기 가이드

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 인시던트 분석: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립을 지원할 수도 있음: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

Azure Security Center의 보안 경고: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

태그를 사용 하 여 Azure 리소스를 구성 합니다. https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

NIST 게시물: IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드 참조: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 &nbsp; 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
    

Azure Security Center 보안 연락처를 설정하는 방법: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

연속 내보내기를 구성하는 방법: https://docs.microsoft.com/azure/security-center/continuous-export

경고를 Azure Sentinel로 스트림하는 방법: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

워크플로 자동화 및 Logic Apps를 구성하는 방법: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 그룹: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행하고, 모든 중요한 보안 결과를 60일 이내에 수정해야 합니다.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 하세요.

Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 Microsoft의 전략 및 실행에 대 한 자세한 내용은 다음에서 확인할 수 있습니다. https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.