---
title: Azure 데이터 탐색기 클러스터에 대해 올바른 계산 SKU를 선택 합니다.
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 대해 최적의 계산 SKU 크기를 선택 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 44c115cd509b72d5f83b1c1109ae09dc050d1a74
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337451"
---
# <a name="select-the-correct-compute-sku-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 올바른 계산 SKU를 선택 합니다. 

새 클러스터를 만들거나 변경 작업에 대 한 클러스터를 최적화 하는 경우 Azure 데이터 탐색기는 여러 VM (가상 머신) Sku를 제공 하 여 선택할 수 있습니다. 이러한 계산 Sku는 작업에 가장 적합 한 비용을 제공 하기 위해 신중 하 게 선택 했습니다. 

데이터 관리 클러스터의 크기 및 VM SKU는 Azure 데이터 탐색기 서비스에 의해 완벽 하 게 관리 됩니다. 엔진의 VM 크기 및 수집 워크 로드와 같은 요인에 따라 결정 됩니다. 

언제 든 지 [클러스터를 확장](manage-cluster-vertical-scaling.md)하 여 엔진 클러스터에 대 한 계산 SKU를 변경할 수 있습니다. 초기 시나리오에 맞는 가장 작은 SKU 크기로 시작 하는 것이 가장 좋습니다. 클러스터를 확장 하면 새 SKU를 사용 하 여 클러스터를 다시 만드는 동안 최대 30 분의 가동 중지 시간이 발생 합니다. [Azure Advisor 권장 사항을](azure-advisor.md) 사용 하 여 계산 SKU를 최적화할 수도 있습니다.

> [!TIP]
> [계산 예약 인스턴스 (RI)](/azure/virtual-machines/windows/prepay-reserved-vm-instances) 는 Azure 데이터 탐색기 클러스터에 적용 됩니다.  

이 문서에서는 다양 한 계산 SKU 옵션을 설명 하 고 가장 적합 한 기술 세부 정보를 제공 합니다.

## <a name="select-a-cluster-type"></a>클러스터 유형 선택

Azure 데이터 탐색기는 두 가지 유형의 클러스터를 제공 합니다.

* **프로덕션**: 프로덕션 클러스터는 엔진 및 데이터 관리 클러스터에 대 한 두 개의 노드를 포함 하며 Azure 데이터 탐색기 [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)로 작동 합니다.

* **개발/테스트 (SLA 없음)**: 개발/테스트 클러스터는 엔진 및 데이터 관리 클러스터에 대 한 단일 노드를 포함 합니다. 이 클러스터 유형은 낮은 인스턴스 수 및 엔진 마크업 요금이 없으므로 가장 저렴 한 비용 구성입니다. 중복성이 부족 하기 때문에이 클러스터 구성에 대 한 SLA는 없습니다.

## <a name="compute-sku-types"></a>계산 SKU 유형

Azure 데이터 탐색기 클러스터는 다양 한 유형의 작업에 대해 다양 한 Sku를 지원 합니다. 각 SKU는 고객의 적절 한 배포 크기를 조정 하는 데 도움이 되는 고유한 SSD 및 CPU 비율을 제공 하 고 기업 분석 워크 로드에 대 한 최적의 솔루션을 구축 합니다.

### <a name="compute-optimized"></a>컴퓨팅 최적화

* 높은 코어의 캐시 비율을 제공 합니다.
* 중소 규모의 데이터 크기를 통한 쿼리 비율이 높은 경우에 적합 합니다.
* 대기 시간이 짧은 i/o를 위한 로컬 SSD

### <a name="heavy-compute"></a>과도 한 계산

* 캐시 비율에 대해 훨씬 더 높은 코어를 제공 하는 AMD Sku입니다.
* 대기 시간이 짧은 i/o를 위한 로컬 SSD

### <a name="storage-optimized"></a>Storage에 최적화

* 엔진 노드당 1TB에서 2TB 사이의 더 큰 저장소에 대 한 옵션입니다.
* 과도 한 계산 쿼리 요구 사항으로 많은 양의 데이터를 저장 해야 하는 워크 로드에 적합 합니다.
* 특정 Sku는 핫 데이터 저장소에 대해 로컬 SSD 대신 엔진 노드에 연결 된 premium storage (관리 디스크)를 사용 합니다.

### <a name="isolated-compute"></a>격리된 컴퓨팅

서버 인스턴스 수준 격리를 필요로 하는 워크 로드를 실행 하는 데 적합 한 SKU입니다.

## <a name="select-and-optimize-your-compute-sku"></a>계산 SKU 선택 및 최적화 

### <a name="select-your-compute-sku-during-cluster-creation"></a>클러스터를 만드는 동안 계산 SKU 선택

Azure 데이터 탐색기 클러스터를 만들 때 계획 된 워크 로드에 대 한 *최적의* VM SKU를 선택 합니다.

다음 특성은 SKU를 선택 하는 데 도움이 될 수도 있습니다.
 
| attribute | 세부 정보 |
|---|---
|**가용성**| 모든 Sku를 모든 지역에서 사용할 수 있는 것은 아닙니다. |
|**코어 당 GB 당 캐시 비용**| 계산 및 과도 한 계산에 최적화 된 높은 비용. 저장소에 최적화 된 Sku의 저렴 한 비용 |
|**예약 인스턴스 (RI) 가격 책정**| RI 할인은 지역 및 SKU에 따라 다릅니다. |  

> [!NOTE]
> Azure 데이터 탐색기 클러스터의 경우 계산 비용은 저장소 및 네트워킹에 비해 클러스터 비용의 가장 중요 한 부분입니다.

### <a name="optimize-your-cluster-compute-sku"></a>클러스터 계산 SKU 최적화

클러스터 계산 SKU를 최적화 하려면 [수직 크기 조정을 구성](manage-cluster-vertical-scaling.md#configure-vertical-scaling) 하 고 [Azure Advisor 권장 사항을](azure-advisor.md)확인 하세요. 

선택할 수 있는 다양 한 계산 SKU 옵션을 사용 하면 시나리오에 대 한 성능 및 핫 캐시 요구 사항에 대 한 비용을 최적화할 수 있습니다. 
* 높은 쿼리 볼륨에 가장 적합 한 성능이 필요한 경우에는 적절 한 SKU가 계산에 최적화 되어야 합니다. 
* 비교적 낮은 쿼리 로드를 사용 하 여 대량의 데이터를 쿼리해야 하는 경우 저장소에 최적화 된 SKU를 사용 하면 비용을 줄이고 여전히 뛰어난 성능을 제공할 수 있습니다.

Small Sku에 대 한 클러스터 당 인스턴스 수가 제한 되므로 RAM이 더 큰 Vm을 사용 하는 것이 좋습니다. 을 사용 하는 쿼리와 같이 RAM 리소스에 더 많은 수요를 주는 일부 쿼리 형식에는 더 많은 RAM이 필요 `joins` 합니다. 따라서 크기 조정 옵션을 고려 하는 경우 더 많은 인스턴스를 추가 하 여 확장 하는 대신 더 큰 SKU로 확장 하는 것이 좋습니다.

## <a name="compute-sku-options"></a>계산 SKU 옵션

다음 표에서는 Azure 데이터 탐색기 클러스터 Vm에 대 한 기술 사양을 설명 합니다.

|**이름**| **범주** | **SSD 크기** | **코어 수** | **RAM** | **Premium storage 디스크 ( &nbsp; 1tb)**| **클러스터당 최소 인스턴스 수** | **클러스터당 최대 인스턴스 수**
|---|---|---|---|---|---|---|---
|Dev (SLA 없음) Standard_D11_v2| 계산에 최적화 | 75 &nbsp; GB    | 1 | &nbsp;14gb | 0 | 1 | 1
|Dev (SLA 없음) Standard_E2a_v4| 계산에 최적화 | &nbsp;18gb    | 1 | &nbsp;14gb | 0 | 1 | 1
|Standard_D11_v2| 계산에 최적화 | 75 &nbsp; GB    | 2 | &nbsp;14gb | 0 | 2 | 8 
|Standard_D12_v2| 계산에 최적화 | 150 &nbsp; GB   | 4 | &nbsp;28gb | 0 | 2 | 16
|Standard_D13_v2| 계산에 최적화 | 307 &nbsp; GB   | 8 | 56 &nbsp; GB | 0 | 2 | 1,000
|Standard_D14_v2| 계산에 최적화 | 614 &nbsp; GB   | 16| 112 &nbsp; GB | 0 | 2 | 1,000
|Standard_E2a_v4| 과도 한 계산 | &nbsp;18gb    | 2 | &nbsp;14gb | 0 | 2 | 8 
|Standard_E4a_v4| 과도 한 계산 | 54 &nbsp; GB   | 4 | &nbsp;28gb | 0 | 2 | 16
|Standard_E8a_v4| 과도 한 계산 | 127 &nbsp; GB   | 8 | 56 &nbsp; GB | 0 | 2 | 1,000
|Standard_E16a_v4| 과도 한 계산 | 273 &nbsp; GB   | 16| 112 &nbsp; GB | 0 | 2 | 1,000
|Standard_DS13_v2 + 1tb &nbsp; &nbsp; PS| 저장소 최적화 | &nbsp;1tb | 8 | 56 &nbsp; GB | 1 | 2 | 1,000
|Standard_DS13_v2 + 2tb &nbsp; &nbsp; PS| 저장소 최적화 | 2Tb &nbsp; | 8 | 56 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + &nbsp; 3tb &nbsp; PS| 저장소 최적화 | &nbsp;3tb | 16 | 112 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + &nbsp; 4tb &nbsp; PS| 저장소 최적화 | &nbsp;4tb | 16 | 112 &nbsp; GB | 4 | 2 | 1,000
|Standard_E8as_v4 + 1tb &nbsp; &nbsp; PS| 저장소 최적화 | &nbsp;1tb | 8 | 56 &nbsp; GB | 1 | 2 | 1,000
|Standard_E8as_v4 + 2tb &nbsp; &nbsp; PS| 저장소 최적화 | 2Tb &nbsp; | 8 | 56 &nbsp; GB | 2 | 2 | 1,000
|Standard_E16as_v4 + &nbsp; 3tb &nbsp; PS| 저장소 최적화 | &nbsp;3tb | 16 | 112 &nbsp; GB | 3 | 2 | 1,000
|Standard_E16as_v4 + &nbsp; 4tb &nbsp; PS| 저장소 최적화 | &nbsp;4tb | 16 | 112 &nbsp; GB | 4 | 2 | 1,000
|Standard_L4s| 저장소 최적화 | 650 &nbsp; GB | 4 | 32 &nbsp; GB | 0 | 2 | 16
|Standard_L8s| 저장소 최적화 | 1.3 &nbsp; TB | 8 | 64 &nbsp; GB | 0 | 2 | 1,000
|Standard_L16s| 저장소 최적화 | 2.6 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000
|Standard_L8s_v2| 저장소 최적화 | 1.7 &nbsp; TB | 8 | 64 &nbsp; GB | 0 | 2 | 1,000
|Standard_L16s_v2| 저장소 최적화 | 3.5 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000
|Standard_E64i1_v3| 격리 된 계산 | 1.1 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000

* Azure 데이터 탐색기 [Listskus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)를 사용 하 여 지역별 업데이트 된 계산 SKU 목록을 볼 수 있습니다. 
* [다양 한 sku](/azure/virtual-machines/windows/sizes)에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

* 서로 다른 요구 사항에 따라 VM SKU를 변경 하 여 언제 든 지 엔진 클러스터를 [확장 하거나](manage-cluster-vertical-scaling.md) 축소할 수 있습니다. 

* 변화 하는 수요에 따라 용량을 변경 하기 위해 엔진 클러스터 크기를 [확장 하거나 축소할](manage-cluster-horizontal-scaling.md) 수 있습니다.

* [Azure Advisor 권장 사항을](azure-advisor.md) 사용 하 여 계산 SKU를 최적화 합니다.