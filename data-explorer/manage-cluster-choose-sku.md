---
title: Azure 데이터 탐색기 클러스터에 대한 올바른 VM SKU 선택
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 대한 최적의 SKU 크기를 선택하는 방법을 설명합니다.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502336"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 적합한 VM SKU 선택 

새 클러스터를 만들거나 변화하는 워크로드에 맞게 클러스터를 최적화하는 경우 Azure Data Explorer는 선택할 수 있는 여러 가상 시스템(VM) SCO를 제공합니다. VM은 모든 워크로드에 가장 적합한 비용을 제공하기 위해 신중하게 선택되었습니다. 

데이터 관리 클러스터의 크기와 VM SKU는 Azure 데이터 탐색기 서비스에서 완전히 관리됩니다. 엔진의 VM 크기 및 흡기 워크로드와 같은 요인에 의해 결정됩니다. 

[클러스터를 확장하여](manage-cluster-vertical-scaling.md)언제든지 엔진 클러스터에 대한 VM SKU를 변경할 수 있습니다. 초기 시나리오에 맞는 가장 작은 SKU 크기로 시작하는 것이 가장 좋습니다. 클러스터를 확장하면 새 VM SKU를 사용하여 클러스터를 다시 만드는 동안 최대 30분의 가동 중지 시간이 발생합니다.

> [!TIP]
> [RI(예약 인스턴스 계산)는](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) Azure 데이터 탐색기 클러스터에 적용할 수 있습니다.  

이 문서에서는 다양한 VM SKU 옵션에 대해 설명하고 최상의 선택을 하는 데 도움이 되는 기술 세부 정보를 제공합니다.

## <a name="select-a-cluster-type"></a>클러스터 유형 선택

Azure 데이터 탐색기는 두 가지 유형의 클러스터를 제공합니다.

* **프로덕션**: 프로덕션 클러스터에는 엔진 및 데이터 관리 클러스터에 대한 두 개의 노드가 포함되며 Azure Data Explorer [SLA에서](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)작동합니다.

* **개발자/테스트(SLA 없음)**: 개발자/테스트 클러스터에는 엔진 클러스터에 대한 단일 D11 v2 노드와 데이터 관리 클러스터의 단일 D1 노드가 있습니다. 이 클러스터 유형은 인스턴스 수가 적고 엔진 마크업 요금이 없기 때문에 가장 낮은 비용 구성입니다. 중복성이 없기 때문에 이 클러스터 구성에는 SLA가 없습니다.

## <a name="sku-types"></a>SKU 유형

Azure 데이터 탐색기 클러스터를 만들 때 계획된 워크로드에 가장 *적합한* VM SKU를 선택합니다. 다음 두 Azure 데이터 탐색기 SKU 제품군 중에서 선택할 수 있습니다.

* **D v2**: D SKU는 컴퓨팅에 최적화되어 있으며 두 가지 맛으로 제공됩니다.
    * VM 자체
    * 프리미엄 스토리지 디스크와 함께 제공되는 VM

* **LS**: L SKU는 스토리지에 최적화되어 있습니다. 비슷한 가격의 D SKU보다 훨씬 더 큰 SSD 크기를 가지고 있습니다.

사용 가능한 SKU 형식 간의 주요 차이점은 다음 표에 설명되어 있습니다.
 
| attribute | D SKU | L SKU |
|---|---|---
|**스몰 SUS**|최소 크기는 두 개의 코어가있는 D11입니다.|최소 크기는 4 개의 코어가있는 L4입니다. |
|**가용성**|모든 지역에서 사용 가능(DS+PS 버전은 가용성이 더 제한적입니다)|일부 지역에서 사용 가능 |
|**코어당&nbsp;GB 캐시당 비용**|D SKU와 높은, DS + PS 버전으로 낮은|종량제 옵션으로 최저 |
|**예약 인스턴스(RI) 가격 책정**|높은 할인 (3&nbsp;년 약정에 대한 55 % 이상)|낮은 할인 (3 년 약정에 대한 20&nbsp;%) |  

## <a name="select-your-cluster-vm"></a>클러스터 VM 선택 

클러스터 VM을 선택하려면 [수직 크기 조정을 구성합니다.](manage-cluster-vertical-scaling.md#configure-vertical-scaling) 

선택할 수 있는 다양한 VM SKU 옵션을 사용하면 시나리오의 성능 및 핫 캐시 요구 사항에 대한 비용을 최적화할 수 있습니다. 
* 쿼리 볼륨이 많은 경우 이상적인 SKU가 계산에 최적화되어야 합니다. 
* 쿼리 로드가 상대적으로 낮은 대용량 데이터를 쿼리해야 하는 경우 스토리지에 최적화된 SKU를 사용하면 비용을 절감하고 우수한 성능을 제공할 수 있습니다.

소규모 SCO의 클러스터당 인스턴스 수는 제한되어 있으므로 RAM이 큰 더 큰 VM을 사용하는 것이 좋습니다. 를 사용하는 `joins`쿼리와 같이 RAM 리소스에 더 많은 수요를 제공하는 일부 쿼리 유형에는 더 많은 RAM이 필요합니다. 따라서 크기 조정 옵션을 고려할 때는 더 많은 인스턴스를 추가하여 확장하지 않고 더 큰 SKU로 확장하는 것이 좋습니다.

## <a name="vm-options"></a>VM 옵션

Azure 데이터 탐색기 클러스터 VM에 대한 기술 사양은 다음 표에 설명되어 있습니다.

|**이름**| **범주** | **SSD 크기** | **코어** | **Ram** | **프리미엄 스토리지 디스크(1TB)&nbsp;**| **클러스터당 최소 인스턴스 수** | **클러스터당 최대 인스턴스 수**
|---|---|---|---|---|---|---|---
|D11 v2| 컴퓨팅 최적화 | 75GB&nbsp;    | 2 | 14GB&nbsp; | 0 | 1 | 8(개발/테스트 SKU 제외, 1)
|D12 v2| 컴퓨팅 최적화 | 150GB&nbsp;   | 4 | 28GB&nbsp; | 0 | 2 | 16
|D13 v2| 컴퓨팅 최적화 | 307GB&nbsp;   | 8 | 56GB&nbsp; | 0 | 2 | 1,000
|D14 v2| 컴퓨팅 최적화 | 614&nbsp;GB   | 16| 112GB&nbsp; | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| 스토리지 에 최적화 | 1&nbsp;TB | 8 | 56GB&nbsp; | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| 스토리지 에 최적화 | 2&nbsp;TB | 8 | 56GB&nbsp; | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| 스토리지 에 최적화 | 3&nbsp;TB | 16 | 112GB&nbsp; | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| 스토리지 에 최적화 | 4&nbsp;TB | 16 | 112GB&nbsp; | 4 | 2 | 1,000
|L4s v1| 스토리지 에 최적화 | 650GB&nbsp; | 4 | 32GB&nbsp; | 0 | 2 | 16
|L8s v1| 스토리지 에 최적화 | 1.3&nbsp;TB | 8 | 64GB&nbsp; | 0 | 2 | 1,000
|L16s_1| 스토리지 에 최적화 | 2.6&nbsp;TB | 16| 128GB&nbsp; | 0 | 2 | 1,000

* Azure 데이터 탐색기 [ListSkus API를](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)사용하여 리전별로 업데이트된 VM SKU 목록을 볼 수 있습니다. 
* [다양한 SCO에](/azure/virtual-machines/windows/sizes)대해 자세히 알아보십시오. 

## <a name="next-steps"></a>다음 단계

* 필요에 따라 VM SKU를 변경하여 언제든지 엔진 클러스터를 [확장하거나 축소할](manage-cluster-vertical-scaling.md) 수 있습니다. 

* 변화하는 요구에 따라 엔진 클러스터의 크기를 [확장하거나 확장하여](manage-cluster-horizontal-scaling.md) 용량을 변경할 수 있습니다.

