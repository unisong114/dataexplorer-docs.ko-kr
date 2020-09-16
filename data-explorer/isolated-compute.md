---
title: Azure 데이터 탐색기 클러스터에서 격리 된 계산 사용
description: 이 문서에서는 올바른 SKU를 선택 하 여 Azure 데이터 탐색기 클러스터에서 격리 된 계산을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: dagrawal
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: references_regions
ms.openlocfilehash: 8139701037a8d4d25490a355cc822051851c85cd
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682315"
---
# <a name="enable-isolated-compute-on-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에서 격리 된 계산 사용

격리 된 계산 Vm (가상 머신)을 사용 하면 고객이 단일 고객 전용의 하드웨어 격리 환경에서 워크 로드를 실행할 수 있습니다. 격리 된 계산 Vm으로 배포 된 클러스터는 규정 준수 및 규정 요구 사항에 대 한 높은 수준의 격리를 필요로 하는 워크 로드에 가장 적합 합니다. 계산 SKU는 구성의 유연성을 저하 시 키 지 않고 데이터를 보호 하기 위해 격리를 제공 합니다. 자세한 내용은 [계산 격리](/azure/security/fundamentals/isolation-choices#compute-isolation) 및 [격리 된 계산에 대 한 Azure 지침](/azure/azure-government/azure-secure-isolation-guidance#compute-isolation)을 참조 하세요. 

Azure 데이터 탐색기는 SKU **Standard_E64i_v3**를 사용 하 여 격리 된 계산에 대 한 지원을 제공 합니다. 이 SKU는 응용 프로그램 또는 엔터프라이즈의 요구에 맞게 자동으로 확장 및 축소할 수 있습니다. 격리 된 계산 지원은 다음 지역에서 제공 됩니다.
* 미국 서부 2
* 미국 동부 
* 미국 중남부

격리 된 계산 Vm은 매우 저렴 하지만 서버 인스턴스 수준 격리를 필요로 하는 워크 로드를 실행 하는 데 이상적인 SKU입니다. Azure 데이터 탐색기에 대해 지원 되는 Sku에 대 한 자세한 내용은 [azure 데이터 탐색기 클러스터에 대 한 올바른 VM SKU 선택](manage-cluster-choose-sku.md)을 참조 하세요.

> [!NOTE]
> Azure [전용 호스트](https://azure.microsoft.com/services/virtual-machines/dedicated-host/) 는 현재 azure 데이터 탐색기에서 지원 되지 않습니다. 

## <a name="enable-isolated-compute-on-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에서 격리 된 계산 사용 

Azure 데이터 탐색기에서 격리 된 계산을 사용 하도록 설정 하려면 다음 프로세스 중 하나를 수행 합니다.
* [격리 된 계산 SKU를 사용 하 여 클러스터 만들기](#create-a-cluster-with-isolated-compute-sku)
* [기존 클러스터에서 격리 된 계산 SKU를 선택 합니다.](#select-the-isolated-compute-sku-on-an-existing-cluster)

## <a name="create-a-cluster-with-isolated-compute-sku"></a>격리 된 계산 SKU를 사용 하 여 클러스터 만들기

1. 지침에 따라 [Azure Portal에서 Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다](create-cluster-database-portal.md) .
1. **기본 사항** 탭 내에서 [클러스터 만들기](create-cluster-database-portal.md#create-a-cluster) 의 **계산 사양** 드롭다운에서 **Standard_E64i_v3** 를 선택 합니다.

## <a name="select-the-isolated-compute-sku-on-an-existing-cluster"></a>기존 클러스터에서 격리 된 계산 SKU를 선택 합니다.

1. Azure 데이터 탐색기 클러스터 **개요** 화면에서 강화 **를 선택 합니다** .
1. 검색 상자에서 *Standard_E64i_v3* 를 검색 하 고 sku 이름을 클릭 하거나 sku 목록에서 **Standard_E64i_v3** sku를 선택 합니다.
1. **적용** 을 선택 하 여 SKU를 업데이트 합니다. 

:::image type="content" source="media/isolated-compute/select-isolated-compute-sku.png" alt-text="격리 된 계산 SKU를 선택 합니다.":::

> [!TIP]
> 수직 확장 프로세스는 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [변경 수요에 맞게 Azure 데이터 탐색기에서 클러스터 수직 확장 (강화) 관리](manage-cluster-vertical-scaling.md)
* [Azure 데이터 탐색기 클러스터에 올바른 VM SKU를 선택 합니다.](manage-cluster-choose-sku.md)
