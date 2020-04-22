---
title: 데이터 탐색기용 Azure 보안 기준
description: 데이터 탐색기용 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e67621e92af12c45ac7e66bd6055f90245f705d2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501322"
---
# <a name="azure-security-baseline-for-data-explorer"></a>데이터 탐색기용 Azure 보안 기준

데이터 탐색기용 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

**안내**: Azure Data Explorer는 가상 네트워크의 서브넷에 클러스터 배포를 지원합니다. 이 기능을 사용하면 Azure Data Explorer 클러스터 트래픽에 NSG(네트워크 보안 그룹) 규칙을 적용하고, 온-프레미스 네트워크를 Azure Data Explorer 클러스터의 서브넷에 연결하고, 서비스 끝점으로 데이터 연결 소스(이벤트 허브 및 이벤트 그리드)를 보호할 수 있습니다.

Azure 데이터 탐색기 클러스터를 가상 네트워크에 배포하는 방법:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NICS의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침**: NSG(네트워크 보안 그룹) 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure 보안 센터에서 제공하는 네트워크 보안 이해:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당되지 않음; 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격으로부터 보호하기 위해 데이터 탐색기 클러스터를 보호하는 가상 네트워크에서 Azure DDoS 보호 표준을 활성화합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

DDoS 보호를 구성하는 방법:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 보안 센터 통합 위협 인텔리전스 이해:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure Data Explorer 클러스터를 보호하는 데 사용되는 NSG(네트워크 보안 그룹)에서 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 배포

**지침**: 해당되지 않음; 이 컨트롤은 끝점 또는 방화벽에서 수행됩니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용하여 Azure Data Explorer 클러스터와 연결된 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

서비스 태그 이해 및 사용:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Azure 데이터 탐색기의 구성 요구 사항에 대한 서비스 태그:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure 정책을 사용하여 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현하는 고객입니다.

고객은 Azure Blueprint를 사용하여 ARM 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의에서 패키징하여 대규모 Azure 배포를 단순화할 수도 있습니다. 새로운 구독 및 환경에 Blueprint를 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: NSG(네트워크 보안 그룹) 및 데이터 탐색기 클러스터의 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크로 의 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 사항 감지

**지침**: Azure 정책을 사용하여 네트워크 리소스에 대한 구성의 유효성을 검사(및/또는 수정)합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Microsoft는 Azure 리소스에 대한 시간 원본을 유지 관리하며 고객은 고객이 소유한 계산 배포에 대한 시간 동기화를 업데이트할 수 있습니다.

Azure 계산 리소스에 대한 시간 동기화를 구성하는 방법:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Data Explorer는 진단 로그를 사용하여 수집 성공 및 실패에 대한 통찰력을 제공합니다. 작업 로그를 Azure 저장소, 이벤트 허브 또는 로그 분석으로 내보내 수집 상태를 모니터링할 수 있습니다.

Azure 데이터 탐색기 수집 작업을 모니터링하는 방법:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure 데이터 탐색기에서 모니터링 데이터를 수집하고 쿼리하는 방법:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 서비스 특정 작업 및 로깅에 액세스하고 로깅하려면 Azure 데이터 탐색기의 진단 설정을 사용하도록 설정합니다. 리소스에 대한 상위 수준 로깅이 포함된 Azure 활동 로그는 기본적으로 활성화됩니다.

Azure 데이터 탐색기 수집 작업을 모니터링하는 방법:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure 모니터를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure 플랫폼 로그 개요:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 로그 분석 작업 영역 보존 기간을 설정합니다. 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

로그 분석 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작이 대두되는 로그를 분석 및 모니터링하고 결과를 정기적으로 검토합니다. Azure 데이터 탐색기의 진단 설정을 사용하도록 설정한 후 Azure 모니터의 로그 분석 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

로그 분석 작업 영역 이해:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 사용자 지정 쿼리를 수행하는 방법:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: 해당되지 않음; Azure 데이터 탐색기에는 이 기능이 없습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않음; Azure Data Explorer는 맬웨어 방지 로깅을 처리하지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 적용 가능 하지 않음: DNS 쿼리Azure 데이터 탐색기의 함수가 아닙니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침**: Azure Data Explorer에서 보안 역할은 데이터베이스 또는 테이블과 같은 보안 리소스에서 작업할 수 있는 보안 주체(사용자 및 응용 프로그램)와 허용되는 작업을 정의합니다.  Kusto 쿼리를 활용하여 Azure Data Explorer 클러스터 및 데이터베이스의 관리자 역할에 원칙을 나열할 수 있습니다.
[Kusto 쿼리를 사용하는 Azure 데이터 탐색기의 보안 역할 관리](kusto/management/security-roles.md)



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure AD에는 기본 암호의 개념이 없습니다. 암호가 필요한 다른 Azure 리소스는 서비스에 따라 달라보이는 복잡성 요구 사항및 최소 암호 길이로 암호를 만들어야 합니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 마켓플레이스 서비스에 대한 책임은 귀하에게 있습니다.


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정의 사용 확인

**안내**: 전용 관리 계정 사용에 대한 표준 운영 절차를 작성하는 고객. Azure 보안 센터 ID 및 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 고객은 Microsoft 서비스 및 Azure ARM에 대해 Azure AD 권한 ID 관리 권한 있는 역할을 사용하여 적시에 / 충분히 액세스할 수 있도록 설정할 수 있습니다. 

Azure AD 권한 ID 관리란 무엇입니까?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory를 사용하여 단일 사인온(SSO) 활용

**지침**: 가능하면 고객은 서비스당 개별 독립 실행형 자격 증명을 구성하는 대신 Azure Active Directory(Azure AD)와 함께 SSO를 사용합니다. Azure 보안 센터 ID 및 액세스 관리 권장 사항을 사용합니다.

Azure AD로 SSO 이해:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증을 사용합니다.

**지침**: Azure Active Directory(Azure AD) 다단계 인증(MFA)을 활성화하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 MFA(다단계 인증)를 사용하여 PAW(권한 있는 액세스 워크스테이션)를 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고 생성을 위해 Azure Active Directory(Azure AD) 보안 보고서를 사용합니다. Azure 보안 센터를 사용하여 ID 및 액세스 활동 모니터링

위험한 활동에 플래그가 지정된 Azure AD 사용자를 식별하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure 보안 센터에서 사용자 ID 및 액세스 활동을 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 고객은 조건부 액세스 명명 된 위치를 사용하여 IP 주소 범위 또는 국가 / 지역의 특정 논리 그룹에서만 액세스 할 수 있습니다.

Azure에서 명명된 위치를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="39-utilize-azure-active-directory"></a>3.9: Azure 활성 디렉터리 활용

**지침**: Azure Active Directory(Azure AD)는 Azure 데이터 탐색기를 인증하는 데 선호되는 방법입니다. 지원하는 여러 인증 시나리오는 다음과 같습니다.

사용자 인증(대화형 로그온): 사용자 주체를 인증하는 데 사용됩니다.

애플리케이션 인증(비대화형 로그온): 사용자가 없는 상태에서 실행/인증해야 하는 서비스 및 애플리케이션을 인증하는 데 사용됩니다.

[Azure 데이터 탐색기 액세스 제어 개요](kusto/management/access-control/index.md)

[Azure Active 디렉터리로 인증](kusto/management/access-control/aad.md)



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스 정기적으로 검토 및 조정

**지침**: Azure Active Directory(Azure AD)는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID Access Reviews를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

[Azure 데이터 탐색기 액세스에 대한 Azure AD로 인증하는 방법](kusto/management/access-control/how-to-authenticate-with-aad.md)

[Azure 광고 보고](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

[Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: 모든 보안 정보 및 이벤트 관리(SIEM) / 모니터링 도구와 통합할 수 있는 모니터링을 위해 Azure Active Directory(Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 소스를 사용할 수 있습니다.

 Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 로그 분석 작업 영역으로 전송하여 이 프로세스를 간소화할 수 있습니다. 고객이 로그 분석 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure 모니터에 통합하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory(Azure AD) 위험 검색 및 ID 보호 기능을 사용하여 사용자 ID와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. 또한 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수 있습니다.

Azure AD 위험한 로그인을 보는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: Microsoft가 고객 데이터에 액세스해야 하는 지원 시나리오에서 Customer Lockbox는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다.

고객 잠금 상자 이해:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적합니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure 데이터 탐색기 클러스터는 가상 네트워크/서브넷으로 다른 리소스와 분리되고 적절하게 태그가 지정되고 NSG(네트워크 보안 그룹) 또는 Azure 방화벽 내에서 보호되어야 합니다. 중요한 데이터를 저장하거나 처리하는 데이터 탐색기 클러스터는 충분히 격리되어야 합니다.

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 데이터 탐색기 클러스터를 가상 네트워크에 배포하는 방법:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

보안 구성을 사용하여 NSG를 만드는 방법:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침**: 해당되지 않음; Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중 모든 민감한 정보 암호화

**지침**: Azure 데이터 탐색기 클러스터는 기본적으로 TLS 1.2를 협상합니다. Azure 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>활성 검색 도구를 사용하여 중요한 데이터 식별</div>

**지침**: Azure 데이터 탐색기에서 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.</div>

**지침**: Azure Data Explorer를 사용하면 RBAC(역할 기반 액세스 제어) 모델을 사용하여 데이터베이스 및 테이블에 대한 액세스를 제어할 수 있습니다. 이 모델에서 주체(사용자, 그룹 및 앱)는 역할에 매핑됩니다. 주체는 자신에게 할당된 역할에 따라 리소스에 액세스할 수 있습니다.

Azure 데이터 탐색기용 RBAC를 구성하는 방법에 대한 역할 및 사용 권한 및 지침 목록:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft는 Azure Data Explorer의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 중민감한 정보 암호화

**지침**: Azure 디스크 암호화는 조직 보안 및 규정 준수 약정을 충족하기 위해 데이터를 보호하고 보호하는 데 도움이 됩니다. 클러스터 가상 시스템의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 또한 Azure Key Vault와 통합되어 디스크 암호화 키 및 비밀을 제어 및 관리하고 Azure Storage에 있는 동안 VM 디스크의 모든 데이터가 암호화되도록 합니다.

Azure 데이터 탐색기 클러스터에 대해 미사용 암호화를 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure 모니터를 사용하여 Azure Data Explorer 클러스터에서 리소스 수준 변경이 수행되는 시기에 대한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: Azure 데이터 탐색기 리소스 보안에 대한 Azure 보안 센터의 권장 사항을 따릅니다.

또한 Microsoft는 Azure 데이터 탐색기를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.

Azure 보안 센터 권장 사항 이해:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 보안 센터 모니터링**: 예

**책임**: 마이크로 소프트

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 활용하여 발견된 취약점의 수정에 우선 순위를 지정합니다.

**지침**: Azure 보안 센터에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.
Azure 보안 센터 보안 점수 이해:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Azure 자산 검색 활용

**지침**: Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트의 적절한(읽기) 권한을 확인하고 구독 내의 모든 Azure 구독및 리소스를 등록합니다.

리소스 그래프를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로 Azure 리소스 관리자 리소스를 만들고 사용하는 것이 좋습니다.

Azure 리소스 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 보는 방법:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해:https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침:** 적절한 경우 적절한 명명 규칙, 태그 지정, 관리 그룹 또는 별도의 구독을 사용하여 자산을 구성하고 추적할 수 있습니다. Azure 리소스 그래프를 사용하여 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 할 수 있습니다. 

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 리소스 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리를 유지 관리합니다.

**지침**: 조직의 필요에 따라 계산 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어의 인벤토리를 만들어야 합니다.  


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 넣을 수 있습니다.

    - 허용되지 않는 리소스 종류

    - 허용되는 리소스 유형

정책 생성 이벤트를 모니터링할 수 있습니다. 

Azure 모니터를 사용하여 모니터링할 수 있는 활동 로그입니다.

또한 Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색할 수 있습니다.

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

빠른 시작: Azure 리소스 그래프 탐색기를 사용하여 첫 번째 리소스 그래프 쿼리를 실행합니다.https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure Monitor를 사용하여 활동 로그 경고를 생성, 보기 및 관리합니다.https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당되지 않음; 이 권장 사항은 전체 리소스 및 Azure를 계산하기 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="68-utilize-only-approved-applications"></a>6.8: 승인된 신청서만 활용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="69-utilize-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 활용

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 넣을 수 있습니다.

    - 허용되지 않는 리소스 종류

    - 허용되는 리소스 유형

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 샘플:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 스크립트를 통해 Azure 리소스 관리자와 상호 작용하는 사용자의 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 Azure 리소스 관리자와 상호 작용하는 기능을 제한합니다. 이렇게 하면 Azure 구독 내에서 리소스를 만들고 변경할 수 없습니다. 

조건부 액세스를 사용하여 Azure 관리에 대한 액세스를 관리합니다.https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용하여 Azure 리소스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure 정책 정의를 사용할 수도 있습니다.

또한 Azure 리소스 관리자는 JSON(JavaScript 개체 표기설정)에서 템플릿을 내보낼 수 있으며, 구성이 조직의 보안 요구 사항을 충족 /초과하는지 확인하기 위해 검토해야 합니다.

Azure 보안 센터의 권장 사항을 Azure 리소스에 대한 보안 구성 기준으로 사용할 수도 있습니다.

사용 가능한 Azure 정책 별칭을 보는 방법:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

단일 및 다중 리소스 Azure 포털의 템플릿으로 내보내기:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

보안 권장 사항 - 참조 가이드:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: 운영 체제에 대한 보안 구성 설정

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: 모든 Azure 리소스에 대한 보안 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.  변경 내용 추적, 정책 준수 대시보드 또는 사용자 지정 솔루션과 같은 솔루션을 사용하여 환경의 보안 변경 사항을 쉽게 식별할 수 있습니다.

Azure 정책 효과 이해:https://docs.microsoft.com/azure/governance/policy/concepts/effects

규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

변경 내용 추적 솔루션을 사용하여 환경의 변경 사항을 추적합니다.https://docs.microsoft.com/azure/automation/change-tracking

Azure 리소스의 규정 준수 데이터 가져오기:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: 운영 체제를 위한 보안 구성 유지

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

**지침**: Azure Repos를 사용하여 사용자 지정 Azure 정책, Azure 리소스 관리자 템플릿, 원하는 상태 구성 스크립트 등과 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 특정 사용자, 기본 제공 보안 그룹 또는 Azure Active Directory(Azure AD)에 정의된 그룹에 대한 권한을 부여하거나 거부할 수 있습니다.

Azure DevOps에 코드를 저장하는 방법:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure DevOps의 사용 권한 및 그룹에 대해:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure 정책을 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련된 기본 제공 정책 정의를 사용할 수도 있습니다.  또한 Azure 자동화를 사용하여 구성 변경 내용을 배포할 수 있습니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

별칭 사용 방법:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure 정책 [감사], [거부]및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="711-securely-manage-azure-secrets"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure 디스크 암호화는 Azure Data Explorer 클러스터 가상 시스템의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 또한 Azure Key Vault와 통합되어 디스크 암호화 키 및 비밀을 제어 및 관리하고 Azure Storage에 있는 동안 VM 디스크의 모든 데이터가 암호화되도록 합니다.

Azure 데이터 탐색기에서 클러스터를 보호하는 방법:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 마이크로 소프트

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: 관리되는 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다. 관리되는 ID를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure 데이터 탐색기 클러스터에 대해 관리되는 ID를 구성합니다.https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다. 

자격 증명 스캐너를 설정하는 방법:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하십시오.*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리되는 맬웨어 방지 소프트웨어 활용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Data Explorer)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비계산 Azure 리소스에 업로드할 파일을 미리 스캔합니다.

**지침**: Azure 서비스(예: Azure Data Explorer)를 지원하는 기본 호스트에서 Microsoft 맬웨어 방지 기능이 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

Azure 데이터 탐색기, 데이터 레이크 저장소, Blob 저장소, PostgreSQL용 Azure 데이터베이스 등과 같이 계산되지 않는 Azure 리소스에 업로드되는 모든 콘텐츠를 미리 검사합니다. 이러한 경우 Microsoft는 사용자의 데이터에 액세스할 수 없습니다.



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하십시오.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

**지침**: 데이터 탐색기 클러스터에서 사용하는 Microsoft Azure 저장소 계정의 데이터는 항상 복제되어 내구성과 고가용성을 보장합니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다.

Azure 저장소 중복성 및 서비스 수준 계약 이해:https://docs.microsoft.com/azure/storage/common/storage-redundancy

[데이터를 저장소로 내보내기](kusto/management/data-export/export-data-to-storage.md)



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 고객 관리 키를 완벽하게 시스템 백업 및 백업 수행

**지침**: Azure Data Explorer는 미사용 저장소 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 데이터 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 고객 관리 키는 Azure 키 자격 증명 모음에 저장해야 합니다. 

C# 을 사용하여 고객 관리 키 구성 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키를 구성합니다.https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Azure 키 볼트 인증서를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure 키 볼트 비밀의 데이터 복원을 주기적으로 테스트합니다.

Azure 키 볼트 인증서를 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

C# 을 사용하여 고객 관리 키 구성 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키를 구성합니다.https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: 고객이 실수로 또는 악의적 인 삭제로부터 키를 보호하기 위해 키 볼트에서 소프트 삭제를 사용할 수 있습니다.  삭제된 상태의 볼트 또는 개체가 보존 기간이 경과할 때까지 제거할 수 없도록 제거 보호를 활성화할 수도 있습니다.  

키 자격 증명 모음에서 소프트 삭제 및 지우기 보호를 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

C# 을 사용하여 고객 관리 키 구성 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키를 구성합니다.https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-incident-response-guide"></a>10.1: 인시던트 대응 가이드 작성

**지침**: 조직에 대한 인시던트 대응 가이드를 작성합니다. 감지에서 사후 검토에 이르는 사고 처리/관리 단계뿐만 아니라 직원의 모든 역할을 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침:** 보안 센터는 각 경고에 심각도를 할당하여 먼저 조사해야 하는 경고의 우선 순위를 지정합니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되는 정기적인 흐름에 따라 시스템의 인시던트 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: 보안 인시던트 연락처 &nbsp;세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** MICROSOFT 보안 대응 센터(MSRC)에서 불법 또는 승인되지 않은 당사자가 데이터에 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다. 사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다.
    

Azure 보안 센터 보안 연락처를 설정 하는 방법:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

**지침**: 지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보내 Azure 리소스에 대한 위험을 식별합니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다. Azure 보안 센터 데이터 커넥터를 사용하여 경고를 Azure Sentinel으로 스트리밍할 수 있습니다.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

**지침**: Azure 보안 센터의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "논리 앱"을 통해 응답을 자동으로 트리거하여 Azure 리소스를 보호합니다.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 빨간색 팀 연습을](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)참조하십시오.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과를 수정할 수 있습니다.

**지침**: 보급 시험이 Microsoft 정책을 위반하지 않도록 Microsoft 참여 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1규칙을 따르십시오.

Microsoft 관리 클라우드 인프라, 서비스 및 응용 프로그램에 대한 Microsoft의 전략 및 Red Teaming 및 라이브 사이트 침투 테스트실행에 대한 자세한 내용은 여기에서 확인할 수 있습니다.https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- Azure [보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 보기
- [Azure 보안 기준에](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 대해 자세히 알아보기
