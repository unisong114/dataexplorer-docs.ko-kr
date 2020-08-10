---
title: Azure 데이터 탐색기을 사용 하 여 비즈니스 연속성 및 재해 복구 솔루션 만들기
description: 이 문서에서는 Azure 데이터 탐색기를 사용 하 여 비즈니스 연속성 및 재해 복구 솔루션을 만드는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 0b37d65d095806d108a632c315820d164f45a520
ms.sourcegitcommit: 39a055e246539ac64d651abb42531892dd4393e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028901"
---
# <a name="create-business-continuity-and-disaster-recovery-solutions-with-azure-data-explorer"></a>Azure 데이터 탐색기을 사용 하 여 비즈니스 연속성 및 재해 복구 솔루션 만들기

이 문서에서는 azure 데이터 탐색기 리소스, 관리 및 수집을 다른 Azure 지역에 복제 하 여 Azure 지역 가동 중단을 준비 하는 방법에 대해 자세히 설명 합니다. 이벤트 허브를 사용 하 여 데이터를 수집 하는 예를 제공 합니다. 비용 최적화는 다양 한 아키텍처 구성에 대해서도 설명 합니다. 아키텍처 고려 사항 및 복구 솔루션에 대해 자세히 살펴보면 [비즈니스 연속성 개요](business-continuity-overview.md)를 참조 하세요.

## <a name="prepare-for-azure-regional-outage-to-protect-your-data"></a>데이터를 보호 하기 위해 Azure 지역 가동 중단 준비

Azure 데이터 탐색기는 전체 Azure 지역의 작동 중단을 방지 하는 자동 보호를 지원 하지 않습니다. 이 중단은 지진과 같은 자연 재해 발생 시 발생할 수 있습니다. 재해 복구 상황을 해결 하기 위한 솔루션이 필요한 경우 다음 단계를 수행 하 여 비즈니스 연속성을 보장 하세요. 이러한 단계에서는 두 개의 Azure 쌍을 이루는 지역에서 클러스터, 관리 및 데이터 수집을 복제 합니다.

1. 두 개의 Azure 쌍을 이루는 지역에 [두 개 이상의 독립 클러스터를 만듭니다](#create-multiple-independent-clusters) .
1. 새 테이블을 만들거나 각 클러스터에서 사용자 역할을 관리 하는 등의 [모든 관리 작업을 복제](#replicate-management-activities) 합니다.
1. 각 클러스터에 데이터를 병렬로 수집 합니다.

### <a name="create-multiple-independent-clusters"></a>여러 독립 클러스터 만들기

둘 이상의 지역에서 둘 이상의 [Azure 데이터 탐색기 클러스터](create-cluster-database-portal.md) 를 만듭니다.
이러한 클러스터 중 두 개 이상이 [Azure 쌍을 이루는 지역](/azure/best-practices-availability-paired-regions)에 만들어졌는지 확인 합니다.

다음 이미지는 세 개의 다른 지역에 있는 세 개의 클러스터 인 복제본을 보여 줍니다. 

:::image type="content" source="media/business-continuity-create-solution/independent-clusters.png" alt-text="독립 클러스터 만들기":::

### <a name="replicate-management-activities"></a>관리 작업 복제

모든 복제본에서 동일한 클러스터 구성을 갖도록 관리 작업을 복제 합니다.

1. 각 복제본에서 동일 하 게 만들기: 
    * 데이터베이스: [Azure Portal](create-cluster-database-portal.md#create-a-database) 또는 microsoft [sdk](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/kusto/Microsoft.Azure.Management.Kusto) 중 하나를 사용 하 여 새 데이터베이스를 만들 수 있습니다.
    * [테이블](kusto/management/create-table-command.md)
    * [매핑](kusto/management/create-ingestion-mapping-command.md)
    * [정책](kusto/management/policies.md)

1. 각 복제본에 대 한 [인증 및 권한 부여](kusto/management/security-roles.md) 를 관리 합니다.

    :::image type="content" source="media/business-continuity-create-solution/regional-duplicate-management.png" alt-text="중복 된 관리 활동":::    

### <a name="configure-data-ingestion"></a>데이터 수집 구성

모든 클러스터에서 데이터 수집을 일관 되 게 구성 합니다. 다음 수집 방법은 다음과 같은 고급 비즈니스 연속성 기능을 사용 합니다.

|수집 방법  |재해 복구 기능  |
|---------|---------|
|[Iot Hub](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)  |[Microsoft에서 시작한 장애 조치 (failover) 및 수동 장애 조치 (failover)](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr) |
|[이벤트 허브](ingest-data-event-hub.md) | [기본 및 보조 재해 복구 네임 스페이스를](/azure/event-hubs/event-hubs-geo-dr) 사용 하 여 메타 데이터 재해 복구     |
|[Event Grid 구독을 사용하여 스토리지에서 수집](ingest-data-event-grid.md) |  이벤트 허브로 전송 되는 blob 생성 메시지에 대 한 [지역 재해 복구](/azure/event-hubs/event-hubs-geo-dr) 를 구현 하 고 [재해 복구 및 계정 장애 조치 (failover) 전략](/azure/storage/common/storage-disaster-recovery-guidance) 을 구현 합니다.       |

## <a name="disaster-recovery-solution-using-event-hub-ingestion"></a>이벤트 허브 수집을 사용 하는 재해 복구 솔루션

[데이터를 보호 하기 위해 Azure 지역 가동 중단 준비](#prepare-for-azure-regional-outage-to-protect-your-data)를 완료 한 후에는 데이터와 관리가 여러 지역에 배포 됩니다. 한 지역에서 중단이 발생 한 경우 Azure 데이터 탐색기는 다른 복제본을 사용할 수 있습니다. 

### <a name="set-up-ingestion-using-event-hub"></a>이벤트 허브를 사용 하 여 수집 설정

다음 예제에서는 이벤트 허브를 통해 수집을 사용 합니다. [장애 조치 (failover) 흐름이](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow) 설정 되었으며, Azure 데이터 탐색기 별칭에서 데이터를 수집 합니다. 클러스터 복제본 당 고유한 소비자 그룹을 사용 하 여 [이벤트 허브에서 데이터를 수집](ingest-data-event-hub.md) 합니다. 그렇지 않으면 트래픽을 복제 하는 대신 배포를 종료 합니다.

> [!NOTE] 
> 이벤트 허브/IoT Hub/저장소를 통한 수집은 매우 강력 합니다. 클러스터를 일정 시간 동안 사용할 수 없는 경우 나중에 다시 파악 하 고 보류 중인 메시지나 blob을 삽입 합니다. 이 프로세스는 [검사점](/azure/event-hubs/event-hubs-features#checkpointing)을 기반으로 합니다.

:::image type="content" source="media/business-continuity-create-solution/event-hub-management-scheme.png" alt-text="이벤트 허브를 통해 수집":::

아래 다이어그램에 표시 된 것 처럼 데이터 원본은 장애 조치 (failover)로 구성 된 이벤트 허브에 이벤트를 생성 하 고 각 Azure 데이터 탐색기 복제본은 이벤트를 사용 합니다. Power BI, Grafana 또는 SDK 구동 웹 앱과 같은 데이터 시각화 구성 요소는 복제본 중 하나를 쿼리할 수 있습니다.

:::image type="content" source="media/business-continuity-create-solution/data-sources-visualization.png" alt-text="데이터 시각화에 대 한 데이터 원본":::

## <a name="optimize-costs"></a>비용 최적화

이제 다음 방법 중 일부를 사용 하 여 복제본을 최적화할 준비가 되었습니다.

* [활성-상시 대기 구성 만들기](#create-an-active-hot-standby-configuration)
* [복제본 시작 및 중지](#start-and-stop-the-replicas)
* [항상 사용 가능한 응용 프로그램 서비스 구현](#implement-a-highly-available-application-service)
* [활성-활성 구성에서 비용 최적화](#optimize-cost-in-an-active-active-configuration)

### <a name="create-an-active-hot-standby-configuration"></a>활성-상시 대기 구성 만들기

Azure 데이터 탐색기 설치를 복제 하 고 업데이트 하면 복제본 수를 사용 하 여 비용이 선형적으로 증가 합니다. 비용을 최적화 하기 위해 아키텍처 변형을 구현 하 여 시간, 장애 조치 (failover) 및 비용의 균형을 맞출 수 있습니다.
활성-상시 대기 구성에서 수동 Azure 데이터 탐색기 복제본을 도입 하 여 비용 최적화를 구현 했습니다. 이러한 복제본은 주 지역 (예: 지역 A)에 재해가 발생 한 경우에만 설정 됩니다. B 및 C 지역에 있는 복제본은 활성 24/7이 될 필요가 없으므로 비용을 크게 줄일 수 있습니다. 그러나 대부분의 경우 이러한 복제본의 성능은 주 클러스터 만큼 적절 하지 않습니다. 자세한 내용은 [활성-상시 대기 구성](business-continuity-overview.md#active-hot-standby-configuration)을 참조 하세요.

아래 이미지에서는 하나의 클러스터만 이벤트 허브에서 데이터를 수집 합니다. 지역 A의 주 클러스터는 저장소 계정에 대 한 모든 데이터의 [연속 데이터 내보내기를](kusto/management/data-export/continuous-data-export.md) 수행 합니다. 보조 복제본은 [외부 테이블](kusto/query/schema-entities/externaltables.md)을 사용 하 여 데이터에 액세스할 수 있습니다.

:::image type="content" source="media/business-continuity-create-solution/active-hot-standby-scheme.png" alt-text="활성/상시 대기 아키텍처":::

### <a name="start-and-stop-the-replicas"></a>복제본 시작 및 중지 

다음 방법 중 하나를 사용 하 여 보조 복제본을 시작 하 고 중지할 수 있습니다.

* [파워 자동화를 위한 Azure 데이터 탐색기 커넥터 (미리 보기)](flow.md)

* Azure Portal의 **개요** 탭에서 **중지** 단추를 클릭 합니다. 자세한 내용은 [클러스터 중지 및 다시 시작](create-cluster-database-portal.md#stop-and-restart-the-cluster)을 참조 하세요.

* Azure CLI: 

```kusto
az kusto cluster stop --name=<clusterName> --resource-group=<rgName> --subscription=<subscriptionId>” 
```

### <a name="implement-a-highly-available-application-service"></a>항상 사용 가능한 응용 프로그램 서비스 구현

#### <a name="create-the-azure-app-service-bcdr-client"></a>Azure App Service BCDR client 만들기

이 섹션에서는 단일 기본 및 여러 보조 Azure 데이터 탐색기 클러스터에 대 한 연결을 지 원하는 [Azure App Service](https://azure.microsoft.com/services/app-service/) 을 만드는 방법을 보여 줍니다. 다음 그림에서는 Azure App Service 설정을 보여 줍니다.

:::image type="content" source="media/business-continuity-create-solution/app-service-setup.png" alt-text="Azure App Service 만들기":::

> [!TIP]
> 동일한 서비스의 복제본 간에 여러 연결을 사용 하면 가용성이 증가 합니다. 이 설치는 지역 가동 중단의 경우에만 유용 합니다.  

1. [App service에이 상용구 코드를](https://github.com/Azure/azure-kusto-bcdr-boilerplate)사용 합니다. 다중 클러스터 클라이언트를 구현 하기 위해 [Adxbcdrclient](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/master/webapp/ADX/AdxBcdrClient.cs) 클래스를 만들었습니다. 이 클라이언트를 사용 하 여 실행 되는 각 쿼리는 [먼저 주 클러스터에](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/26f8c092982cb8a3757761217627c0e94928ee07/webapp/ADX/AdxBcdrClient.cs#L69)전송 됩니다. 오류가 있는 경우 쿼리가 보조 복제본으로 전송 됩니다.

1. [사용자 지정 application insights 메트릭을](/azure/azure-monitor/app/api-custom-events-metrics) 사용 하 여 성능을 측정 하 고 기본 및 보조 클러스터에 대 한 배포를 요청할 수 있습니다. 

#### <a name="test-the-azure-app-service-bcdr-client"></a>Azure App Service BCDR client 테스트

여러 Azure 데이터 탐색기 복제본을 사용 하 여 테스트를 실행 했습니다. 기본 및 보조 클러스터가 시뮬레이션 된 후에는 app service BCDR 클라이언트가 의도 한 대로 작동 하는 것을 볼 수 있습니다.

:::image type="content" source="media/business-continuity-create-solution/simulation-verify-service.png" alt-text="App service BCDR 클라이언트 확인":::

Azure 데이터 탐색기 클러스터는 유럽 서부 (2xD14v2 기본), 남부 동아시아 및 미국 동부 (2Xd14v2)에 배포 됩니다. 

:::image type="content" source="media/business-continuity-create-solution/performance-test-query-time.png" alt-text="교차 행성 쿼리 응답 시간":::

> [!NOTE]
> 응답 시간이 느려지는 이유는 Sku와 교차 행성 쿼리가 서로 다르기 때문입니다.

#### <a name="perform-dynamic-or-static-routing"></a>동적 또는 정적 라우팅을 수행 합니다.

요청의 동적 또는 정적 라우팅을 위해 [Azure Traffic Manager 라우팅 메서드](/azure/traffic-manager/traffic-manager-routing-methods) 를 사용 합니다.  Azure Traffic Manager는 app service 트래픽을 배포할 수 있게 해 주는 DNS 기반 트래픽 부하 분산 장치입니다. 이 트래픽은 글로벌 Azure 지역에서 서비스에 최적화 되어 있으며 고가용성 및 응답성을 제공 합니다. 

[Azure 전면 도어 기반 라우팅을](/azure/frontdoor/front-door-routing-methods)사용할 수도 있습니다. 이러한 두 가지 방법을 비교 하려면 [Azure의 응용 프로그램 배달 도구 모음을 사용한 부하 분산](/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)을 참조 하세요.

### <a name="optimize-cost-in-an-active-active-configuration"></a>활성-활성 구성에서 비용 최적화

재해 복구를 위해 활성-활성 구성을 사용 하면 비용이 선형적으로 늘어납니다. 비용 [에는 노드에 대 한](https://azure.microsoft.com/pricing/details/bandwidth/)노드, 저장소, 마크업 및 향상 된 네트워킹 비용이 포함 됩니다.

#### <a name="use-optimized-autoscale-to-optimize-costs"></a>최적화 된 자동 크기 조정을 사용 하 여 비용 최적화

최적화 된 [자동 크기](manage-cluster-horizontal-scaling.md#optimized-autoscale) 조정 기능을 사용 하 여 보조 클러스터의 가로 크기 조정을 구성 합니다. 수집 부하를 처리할 수 있도록 차원을 지정 해야 합니다. 주 클러스터에 연결할 수 없는 경우 보조 클러스터는 구성에 따라 더 많은 트래픽 및 규모를 가져옵니다. 

이 예에서는 최적화 된 자동 크기 조정 사용을 통해 모든 복제본에서 동일한 가로 및 세로 비율을 사용 하는 것과 비교 하 여 비용의 약 50%를 저장 했습니다.

## <a name="next-steps"></a>다음 단계

[비즈니스 연속성 및 재해 복구 개요](business-continuity-overview.md)를 시작 하세요.
