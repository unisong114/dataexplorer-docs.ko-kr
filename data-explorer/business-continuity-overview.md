---
title: Azure 데이터 탐색기 및 비즈니스 연속성 재해 복구
description: 이 문서에서는 중단 이벤트에서 복구 하기 위한 Azure 데이터 탐색기 기능에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 23f25ca0d32cf241f25a9ac09081dd6c77e072f2
ms.sourcegitcommit: 83202ec6fec0ce98fdf993bbb72adc985d6d9c78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87877197"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>비즈니스 연속성 및 재해 복구 개요

Azure 데이터 탐색기의 비즈니스 연속성 및 재해 복구를 통해 비즈니스는 중단 발생 시 계속 작동할 수 있습니다. 이 문서에서는 가용성 (지역 내) 및 재해 복구에 대해 설명 합니다. 복원 력 있는 Azure 데이터 탐색기 배포에 대 한 기본 기능 및 아키텍처 고려 사항을 자세히 설명 합니다. 사용자 오류, 고가용성 및 여러 재해 복구 구성에서 복구 하는 방법에 대해 자세히 설명 합니다. 이러한 구성은 RPO (복구 지점 목표) 및 RTO (복구 시간 목표), 필요한 작업량, 비용 등의 복원 력 요구 사항에 따라 달라 집니다.

## <a name="mitigate-disruptive-events"></a>중단 이벤트 완화

* [인간 오류](#human-error)
* [Azure 데이터 탐색기의 고가용성](#high-availability-of-azure-data-explorer)
* [Azure 가용성 영역 중단](#outage-of-an-azure-availability-zone)
* [Azure 데이터 센터의 가동 중단](#outage-of-an-azure-datacenter)
* [Azure 지역의 중단](#outage-of-an-azure-region)

### <a name="human-error"></a>인간 오류 

사용자 오류는 피할 수 있습니다. 사용자는 실수로 클러스터, 데이터베이스 또는 테이블을 삭제할 수 있습니다.  

#### <a name="accidental-cluster-or-database-deletion"></a>실수로 인 한 클러스터 또는 데이터베이스 삭제

실수로 인 한 클러스터 또는 데이터베이스 삭제는 복구할 수 없는 작업입니다. Azure 데이터 탐색기 리소스 소유자는 Azure 리소스 수준에서 사용 가능한 삭제 [잠금](/azure/azure-resource-manager/management/lock-resources) 기능을 사용 하도록 설정 하 여 데이터 손실을 방지할 수 있습니다.

#### <a name="accidental-table-deletion"></a>실수로 테이블 삭제

테이블 관리자 권한이 있는 사용자는 [테이블을 삭제할](kusto/management/drop-table-command.md)수 있습니다. 이러한 사용자 중 하나가 실수로 테이블을 삭제 하는 경우 명령을 사용 하 여 복구할 수 있습니다 [`.undo drop table`](kusto/management/undo-drop-table-command.md) . 이 명령을 성공적으로 실행 하려면 먼저 [보존 정책](kusto/management/retentionpolicy.md)에서 *복구* 가능 속성을 사용 하도록 설정 해야 합니다.

#### <a name="accidental-external-table-deletion"></a>실수로 외부 테이블 삭제

[외부 테이블](kusto/query/schema-entities/externaltables.md) 은 데이터베이스 외부에 저장 된 데이터를 참조 하는 스키마 엔터티를 쿼리 하는 데 사용할 수 있습니다. 외부 테이블을 삭제 하면 테이블 메타 데이터만 삭제 됩니다. 테이블 생성 명령을 다시 실행 하 여 복구할 수 있습니다. [일시 삭제](/azure/storage/blobs/storage-blob-soft-delete) 기능을 사용 하 여 사용자가 구성한 시간 동안 실수로 파일/b s/blob을 삭제 하거나 덮어쓰지 않도록 보호 합니다.

### <a name="high-availability-of-azure-data-explorer"></a>Azure 데이터 탐색기의 고가용성

고가용성은 azure 지역 내에서 Azure 데이터 탐색기, 해당 구성 요소 및 기본 종속성의 내결함성을 나타냅니다. 이 내결함성을 통해 구현에서 SPOF (단일 실패 지점이 방지 됩니다. Azure 데이터 탐색기의 고가용성에는 지 속성 계층, 계산 계층 및 리더-종동체 구성이 포함 됩니다.

#### <a name="persistence-layer"></a>지 속성 계층

Azure 데이터 탐색기는 Azure Storage를 내구성이 있는 지 속성 계층으로 활용 합니다. Azure Storage는 자동으로 내결함성을 제공 하며, 기본 설정은 데이터 센터 내에서 LRS (로컬 중복 저장소)를 제공 합니다. 3 개의 복제본이 유지 됩니다. 사용 중인 복제본이 손실 되 면 다른 복제본은 중단 없이 배포 됩니다. 추가 복원 력은 최대 내결함성을 위해 Azure 지역 가용성 영역에서 복제본을 지능적으로 배치 하는 영역 중복 저장소로 수행할 수 있습니다.

#### <a name="compute-layer"></a>계산 계층

Azure 데이터 탐색기는 분산 컴퓨팅 플랫폼 이며, 크기 조정 및 노드 역할 유형에 따라 두 개 이상의 노드가 있을 수 있습니다. 프로 비전 타임에 최대 지역 내 복원 력에 대해 영역 전체에서 노드 배포를 배포 하려면 가용성 영역을 선택 합니다. 가용성 영역 오류가 발생 해도 전체 중단이 발생 하지는 않지만 영역을 복구할 때까지 성능이 저하 됩니다. 

#### <a name="leader-follower-cluster-configuration"></a>리더-종동체 클러스터 구성

Azure 데이터 탐색기는 리더의 데이터 및 메타 데이터에 대 한 읽기 전용 액세스를 위해 리더 클러스터와 다른 종동체 클러스터를 차례로 이동할 수 있는 선택적 [종동체 기능](follower.md) 을 제공 합니다. , 및와 같은 리더의 변경 `create` 사항은 `append` `drop` 자동으로 종동체와 동기화 됩니다. 리더가 Azure 지역에 걸쳐 있을 수 있는 반면, 종동체 클러스터는 리더와 동일한 지역에 호스팅해야 합니다. 리더 클러스터가 다운 되거나 데이터베이스나 테이블이 실수로 삭제 된 경우 리더에서 액세스가 복구 될 때까지 종동체 클러스터는 액세스 권한을 잃게 됩니다. 

### <a name="outage-of-an-azure-availability-zone"></a>Azure 가용성 영역 중단

Azure 가용성 영역은 동일한 Azure 지역 내에서 고유한 물리적 위치입니다. 이러한 사용자는 부분 지역 장애 로부터 Azure 데이터 탐색기 클러스터의 계산 및 데이터를 보호할 수 있습니다. 영역 오류는 지역 내에서 사용 되는 가용성 시나리오입니다. 

Azure 데이터 탐색기 클러스터를 연결 된 다른 Azure 리소스와 동일한 영역에 고정 합니다. 가용성 영역을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [클러스터 만들기](create-cluster-database-portal.md#create-a-cluster)를 참조 하세요.

> [!NOTE] 
> 가용성 영역 선택은 클러스터를 만들 때만 지원 되며 나중에 수정할 수 없습니다.

### <a name="outage-of-an-azure-datacenter"></a>Azure 데이터 센터의 가동 중단

Azure 가용성 영역에는 비용과 함께 제공 되며 일부 고객은 영역 중복성 없이 배포를 선택 합니다. 이러한 Azure 데이터 탐색기 배포를 사용 하면 Azure 데이터 센터 가동 중단으로 인해 클러스터가 중단 됩니다. 따라서 Azure 데이터 센터 가동 중단을 처리 하는 것은 Azure 지역 가동 중단의 경우와 동일 합니다.

### <a name="outage-of-an-azure-region"></a>Azure 지역의 중단

Azure 데이터 탐색기는 전체 Azure 지역의 작동 중단을 방지 하는 자동 보호 기능을 제공 하지 않습니다. [Azure에서 쌍](/azure/best-practices-availability-paired-regions)으로 연결 된 지역에 여러 azure 데이터 탐색기 클러스터가 있는 경우 비즈니스 영향을 최소화 하기 위해 RTO (복구 시간 목표), RPO (복구 지점 목표) 및 노력과 비용 고려 사항에 따라 [여러 가지 재해 복구 구성이](#disaster-recovery-configurations)있습니다. Azure Advisor 권장 사항 및 [자동 크기 조정](manage-cluster-horizontal-scaling.md) 구성에서 비용 및 성능 최적화를 수행할 수 있습니다.

## <a name="disaster-recovery-configurations"></a>재해 복구 구성

 이 섹션에서는 복원 력 요구 사항 (RPO 및 RTO), 필요한 활동 및 비용에 따라 여러 재해 복구 구성에 대해 자세히 설명 합니다.

RTO (복구 시간 목표)는 중단에서 복구 하는 시간을 나타냅니다. 예를 들어 2 시간 RTO는 응용 프로그램이 중단 된 후 2 시간 내에 실행 되어야 한다는 것을 의미 합니다. RPO (복구 지점 목표)는 해당 기간 동안 손실 되는 데이터의 양이 허용 되는 임계값을 초과 하기 전에 중단 중에 통과할 수 있는 시간 간격을 나타냅니다. 예를 들어 RPO가 24 시간이 고 응용 프로그램의 데이터가 15 년 전부터 시작 되는 경우이는 여전히 합의 된 RPO의 매개 변수 내에 있습니다.

수집, 처리 및 큐 레이 션 프로세스에는 재해 복구를 계획할 때 철저 디자인이 필요 합니다. 수집은 다양 한 원본에서 Azure 데이터 탐색기에 통합 되는 데이터를 나타냅니다. 처리는 변형 및 유사한 활동을 의미 합니다. 큐 레이 션은 구체화 된 뷰, data lake로 내보내기 등을 나타냅니다.

다음은 널리 사용 되는 재해 복구 구성입니다. 각 구성에 대해서는 아래에서 자세히 설명 합니다.
* [Always on 구성](#always-on-configuration)
* [활성-활성 구성](#active-active-configuration)
* [활성-상시 대기 구성](#active-hot-standby-configuration)
* [주문형 데이터 복구 클러스터 구성](#on-demand-data-recovery-configuration)

### <a name="always-on-configuration"></a>Always on 구성

작동 중단에 대 한 허용 범위가 없는 중요 응용 프로그램 배포의 경우 Azure 쌍을 이루는 지역에서 여러 Azure 데이터 탐색기 클러스터를 사용 해야 합니다. 모든 클러스터에 대 한 수집, 처리 및 작업을 병렬로 설정 합니다. 클러스터 SKU는 지역에서 동일 해야 합니다. Azure는 Azure 쌍을 이루는 지역에서 업데이트가 롤아웃 되 고 지연 되도록 합니다. Azure 지역 가동 중단으로 인해 응용 프로그램이 중단 되지 않습니다. 약간의 대기 시간 또는 성능 저하가 발생할 수 있습니다.

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="활성-활성-활성-n 구성":::

| **Configuration** | **RPO** | **RTO** | **드는** | **비용** |
| --- | --- | --- | --- | --- |
| **활성-활성-활성-n** | 0 시간 | 0 시간 | 더 낮음 | 가장 높음 |

### <a name="active-active-configuration"></a>활성-활성 구성

이 구성은 [Always on 구성과](#always-on-configuration)동일 하지만 두 개의 Azure 쌍의 지역만 포함 합니다. 이중 수집, 처리 및 curation를 구성 합니다. 사용자는 가장 가까운 지역으로 라우팅됩니다. 클러스터 SKU는 지역에서 동일 해야 합니다.

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="활성-활성 구성":::

| **Configuration** | **RPO** | **RTO** | **드는** | **비용** |
| --- | --- | --- | --- | --- |
| **활성-활성** | 없음 | 없음 | 더 낮음 | 높은 |

### <a name="active-hot-standby-configuration"></a>활성-상시 대기 구성

활성-활성 구성은 이중 수집, 처리 및 curation의 [활성-활성 구성과](#active-active-configuration) 유사 합니다. 그러나 대기 클러스터는 최종 사용자에 게 오프 라인 상태 이며 주 복제본과 동일한 SKU에 있을 필요는 없습니다. 또한 상시 대기 클러스터는 더 작은 SKU 및 규모를 사용할 수 있으므로 성능이 떨어집니다. 재해 시나리오에서는 대기 클러스터가 온라인 상태가 되 고 확장 됩니다.

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="활성-상시 대기 구성":::

| **Configuration** | **RPO** | **RTO** | **드는** | **비용** |
| --- | --- | --- | --- | --- |
| **활성-상시 대기** | 낮음 | 낮음 | 중간 | 중간 |

### <a name="on-demand-data-recovery-configuration"></a>주문형 데이터 복구 구성

이 솔루션은 최소한의 복원 력 (가장 높은 RPO 및 RTO)을 제공 하며, 가장 저렴 하 고 노력이 가장 높습니다. 이 구성에는 데이터 복구 클러스터가 없습니다. GRS (지역 중복 저장소)로 구성 된 저장소 계정에 대 한 큐 레이트 데이터의 연속 내보내기 (raw 및 중간 데이터도 필요 하지 않은 경우)를 구성 합니다. 재해 복구 시나리오가 있는 경우 데이터 복구 클러스터가 분리 됩니다. 이 시점에서 DDLs, 구성, 정책 및 프로세스가 적용 됩니다. 수집 속성이 [kustoCreationTime](kusto/management/data-ingestion/eventgrid.md) 인 저장소에서 데이터를 수집 하 여 기본적으로 시스템 시간에 대 한 수집 시간을 수집 합니다. 

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="주문형 데이터 복구 클러스터 구성":::

| **Configuration** | **RPO** | **RTO** | **드는** | **비용** |
| --- | --- | --- | --- | --- |
| **주문형 데이터 복구 클러스터** | 가장 높음 | 가장 높음 | 가장 높음 | 가장 낮음 |

### <a name="summary-of-disaster-recovery-configuration-options"></a>재해 복구 구성 옵션 요약

| **Configuration** | **복원력** | **RPO** | **RTO** | **드는** | **비용** |
| --- | --- | --- | --- | --- | --- |
| **활성-활성-활성-n** | 가장 높음 | 0 시간 | 0 시간 | 더 낮음 | 가장 높음 |
| **활성-활성** | 높은 | 없음 | 없음 | 더 낮음 | 높은 |
| **활성-상시 대기** | 중간 | 낮음 | 낮음 | 중간 | 중간 |
| **주문형 데이터 복구 클러스터** | 가장 낮음 | 가장 높음 | 가장 높음 | 가장 높음 | 가장 낮음 |

## <a name="best-practices"></a>모범 사례

선택한 재해 복구 구성에 관계 없이 다음 모범 사례를 따르세요.

* 모든 데이터베이스 개체, 정책 및 구성은 원본 제어에서 유지 되므로 릴리스 자동화 도구에서 클러스터로 해제할 수 있습니다. 자세한 내용은 azure [데이터 탐색기에 대 한 Azure DevOps 지원](devops.md)을 참조 하세요. 
* 유효성 검사 루틴을 디자인, 개발 및 구현 하 여 모든 클러스터가 데이터 관점에서 동기화 되도록 합니다. Azure 데이터 탐색기는 [크로스 클러스터 조인을](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer)지원 합니다. 테이블 간의 단순 개수 또는 행은 유효성 검사에 도움이 될 수 있습니다.
* [연속 내보내기](kusto/management/data-export/continuous-data-export.md) 기능을 사용 하 여 Azure 데이터 탐색기 테이블 내에서 Azure Data Lake 저장소로 데이터를 내보냅니다. 가장 높은 복원 력에 대해 GRS을 선택 해야 합니다.
* 릴리스 절차는 클러스터의 미러링을 보장 하는 거 버 넌 스 검사 및 잔액을 포함 해야 합니다.
* 클러스터를 처음부터 빌드하기 위해 수행 하는 작업을 완전히 cognizant 합니다.
* 배포 단위의 검사 목록을 만듭니다. 목록은 사용자의 요구에 따라 고유 하지만 배포 스크립트, 수집 연결, BI 도구 및 기타 중요 한 구성만 포함 해야 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기로 비즈니스 연속성 및 재해 복구 솔루션 만들기](business-continuity-create-solution.md)를 사용 하 여 자세히 알아보세요.
