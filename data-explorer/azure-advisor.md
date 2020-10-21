---
title: Azure Advisor 권장 사항을 사용 하 여 Azure 데이터 탐색기 클러스터 최적화
description: 이 문서에서는 Azure 데이터 탐색기 클러스터를 최적화 하는 데 사용 되는 Azure Advisor 권장 사항을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: lizlotor
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/14/2020
ms.openlocfilehash: 1d7fafcab3293a66bafb4b60f86413d00ee9c354
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241884"
---
# <a name="use-azure-advisor-recommendations-to-optimize-your-azure-data-explorer-cluster-preview"></a>Azure Advisor 권장 사항을 사용 하 여 Azure 데이터 탐색기 클러스터 최적화 (미리 보기)

Azure Advisor는 Azure 데이터 탐색기 클러스터 구성 및 사용량 원격 분석을 분석 하 고 클러스터를 최적화 하는 데 도움이 되는 개인 설정 및 실행 가능한 권장 사항을 제공 합니다.

## <a name="access-the-azure-advisor-recommendations"></a>Azure Advisor 권장 사항에 액세스

Azure Advisor 권장 사항에 액세스 하는 방법에는 두 가지가 있습니다.

### <a name="view-azure-advisor-recommendations-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항 보기

1. Azure Portal에서 Azure 데이터 탐색기 클러스터 페이지로 이동 합니다. 
1. 왼쪽 메뉴의 **모니터링**아래에서 **Advisor 권장 사항**을 선택 합니다. 해당 클러스터에 대 한 권장 구성 목록이 열립니다.

    :::image type="content" source="media/azure-advisor/resource-group-advisor-recommendations.png" alt-text="Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항"::: 

### <a name="view-azure-advisor-recommendations-for-all-clusters-in-your-subscription"></a>구독의 모든 클러스터에 대 한 Azure Advisor 권장 사항 보기

1. Azure Portal에서 [Advisor 리소스로](https://ms.portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)이동 합니다. 
1. **개요**에서 권장 사항을 보려는 구독을 선택 합니다. 
1. 두 번째 드롭다운에서 **azure 데이터 탐색기 클러스터** 및 **azure 데이터 탐색기 데이터베이스** 를 선택 합니다.
 
    :::image type="content" source="media/azure-advisor/advisor-resource.png" alt-text="Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항":::

## <a name="use-the-azure-advisor-recommendations"></a>Azure Advisor 권장 사항 사용

다양 한 Azure Advisor 권장 사항 유형이 있습니다. 클러스터를 최적화 하는 데 도움이 되는 관련 권장 사항 유형을 사용 합니다. 

1. **Advisor**의 **권장 사항**에서 비용 권장 사항에 대 한 **비용** 을 선택 합니다.

    :::image type="content" source="media/azure-advisor/select-recommendation-type.png" alt-text="Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항":::

1. 목록에서 권장 사항을 선택 합니다. 

    :::image type="content" source="media/azure-advisor/select-recommendation.png" alt-text="Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항":::

1. 다음 창에는 권장 사항이 관련 된 클러스터 목록이 포함 되어 있습니다. 권장 사항 세부 정보는 모든 클러스터에 대해 다르며 권장 조치를 포함 합니다.

    :::image type="content" source="media/azure-advisor/clusters-with-recommendations.png" alt-text="Azure 데이터 탐색기 클러스터에 대 한 Azure Advisor 권장 사항":::

## <a name="recommendation-types"></a>권장 사항 유형

비용 및 성능 권장 사항은 현재 사용할 수 있습니다.

> [!IMPORTANT]
> 실제 연간 절감 액은 다를 수 있습니다. 매년 절감 액은 ' 종 량 제 ' 가격을 기준으로 합니다. 잠재적 저장은 Azure Reserved Virtual Machine Instance (RIs) 청구 할인이 고려 하지 않습니다.

### <a name="cost-recommendations"></a>비용 권장 사항

**비용** 권장 사항은 성능 저하 없이 비용을 줄이기 위해 변경 될 수 있는 클러스터에 사용할 수 있습니다. 권장 비용은 다음과 같습니다. 

* [사용 하지 않는 Azure 데이터 탐색기 클러스터](#azure-data-explorer-unused-cluster)
* [활동이 적은 데이터를 포함 하는 Azure 데이터 탐색기 클러스터](#azure-data-explorer-clusters-containing-data-with-low-activity)
* [Azure 데이터 탐색기 클러스터 크기를 적절히 조정 하 여 비용을 최적화 합니다.](#correctly-size-azure-data-explorer-clusters-to-optimize-cost)
* [Azure Data Explorer 테이블의 캐시 줄이기](#reduce-cache-for-azure-data-explorer-tables)

#### <a name="azure-data-explorer-unused-cluster"></a>사용 하지 않는 Azure 데이터 탐색기 클러스터

지난 30 일 동안 적은 양의 데이터, 쿼리 및 수집 이벤트, 지난 2 일간의 CPU 사용량 및 지난 하루에 팔로 워가 없는 경우 클러스터는 사용 되지 않는 것으로 간주 됩니다. **비어 있거나 사용 하지 않는** 클러스터를 삭제 하는 것이 좋습니다. 사용 하지 않는 클러스터를 삭제 하는 것이 좋습니다.

#### <a name="azure-data-explorer-clusters-containing-data-with-low-activity"></a>활동이 적은 데이터를 포함 하는 Azure 데이터 탐색기 클러스터

권장 사항은 데이터를 포함 하지만 활동이 적은 클러스터에 대 한 **비용을 줄이고 데이터를 유지 하기 위해 Azure 데이터 탐색기 클러스터를 중지 하** 는 것입니다. 낮은 활동은 지난 30 일 동안 적은 수의 쿼리 및 ingestions, 지난 2 일간의 낮은 CPU 사용량, 지난 날에는 팔로 워가 없습니다. 클러스터를 중지 하 여 비용을 줄이는 것이 좋습니다. 단, 데이터는 그대로 유지 됩니다. 데이터가 필요 하지 않은 경우 클러스터를 삭제 하 여 절감 액을 높이는 것이 좋습니다.

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-cost"></a>비용을 최적화 하기 위해 Azure 데이터 탐색기 클러스터의 크기를 올바르게 조정

**최적의 비용으로 Azure 데이터 탐색기 클러스터** 의 권장 크기는 크기 또는 VM SKU가 비용을 최적화 하지 않는 클러스터에 제공 됩니다. 이 권장 사항은 데이터 용량, CPU 및 지난 주 동안의 수집 사용량과 같은 매개 변수를 기반으로 합니다. [규모 확장](manage-cluster-vertical-scaling.md) 및 [규모 확장](manage-cluster-horizontal-scaling.md)을 사용 하 여 권장 클러스터 구성으로 크기를 조정 하 여 비용을 줄일 수 있습니다.

[최적화 된 자동 크기 조정 구성을](manage-cluster-horizontal-scaling.md#optimized-autoscale)사용 하는 것이 좋습니다. 최적화 된 자동 크기 조정을 사용 하는 경우 클러스터에 크기 권장 사항이 표시 되 면 현재 VM SKU 또는 최적화 된 자동 크기 조정 최소 및 최대 인스턴스 수 경계가 최적화 되지 않습니다. 권장 되는 인스턴스 수는 정의 된 경계에 포함 되어야 합니다. 자세한 내용은 [VM sku](manage-cluster-choose-sku.md) 및 [가격 책정](https://azure.microsoft.com/pricing/details/data-explorer/)을 참조 하세요.

> [!TIP]
> 최적화 된 자동 크기 조정 구성은 인스턴스 수를 즉시 변경 하지 않습니다. 즉각적인 변경을 위해 [수동 크기 조정을](manage-cluster-horizontal-scaling.md#manual-scale) 사용 하 여 권장 되는 인스턴스 수를 다시 설정한 후 최적화 된 자동 크기 조정을 사용 하도록 설정 합니다.

#### <a name="reduce-cache-for-azure-data-explorer-tables"></a>Azure Data Explorer 테이블의 캐시 줄이기

클러스터 **비용 최적화 권장 사항에 대 한 Azure 데이터 탐색기 테이블 캐시 기간 단축** 은 해당 테이블의 [캐시 정책을](kusto/management/cachepolicy.md)줄일 수 있는 클러스터에 대해 제공 됩니다. 이 권장 사항은 지난 30 일 동안의 쿼리 조회 기간을 기반으로 합니다. 캐시 공간이 절약 되는 상위 10 개 테이블이 표시 됩니다. 이 권장 사항은 클러스터가 캐시 정책 변경을 따라 확장 하거나 축소할 수 있는 경우에만 제공 됩니다. Advisor는 클러스터가 낮은 CPU와 낮은 수집 사용률을 포함 하 고 있음을 확인 합니다. 그러나 데이터 용량이 높아 클러스터를 확장 하거나 축소할 수 없습니다.

### <a name="performance-recommendations"></a>성능 권장 사항

**성능** 권장 사항은 Azure 데이터 탐색기 클러스터의 성능을 향상 시키는 데 도움이 됩니다. 성능 권장 사항은 다음과 같습니다. 
* [Azure 데이터 탐색기 클러스터의 크기를 올바르게 조정 하 여 성능 최적화](#correctly-size-azure-data-explorer-clusters-to-optimize-performance)
* [Azure 데이터 탐색기 테이블에 대 한 업데이트 캐시 정책](#update-cache-policy-for-azure-data-explorer-tables)

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-performance"></a>Azure 데이터 탐색기 클러스터의 크기를 올바르게 조정 하 여 성능 최적화

성능을 최적화 **하기 위한 권장 크기의 Azure 데이터 탐색기 클러스터** 는 크기 또는 VM SKU가 성능 최적화 되지 않는 클러스터에 제공 됩니다. 이 권장 사항은 데이터 용량, 지난 주 동안의 CPU 및 수집 사용량과 같은 매개 변수를 기반으로 합니다. [수직 확장](manage-cluster-vertical-scaling.md) 및 [수평 확장](manage-cluster-horizontal-scaling.md)을 사용 하 여 권장 클러스터 구성의 크기를 올바르게 조정 함으로써 성능을 향상 시킬 수 있습니다.

[최적화 된 자동 크기 조정 구성을](manage-cluster-horizontal-scaling.md#optimized-autoscale)사용 하는 것이 좋습니다. 최적화 된 자동 크기 조정을 사용 하는 경우 클러스터에 대 한 크기 권장 사항이 표시 되 면 현재 VM SKU 또는 최적화 된 자동 크기 조정 최소 및 최대 인스턴스 수 경계가 최적화 되지 않습니다. 권장 되는 인스턴스 수는 정의 된 경계에 포함 되어야 합니다. 자세한 내용은 [VM sku](manage-cluster-choose-sku.md)를 참조 하세요.

> [!TIP]
> 최적화 된 자동 크기 조정 구성은 인스턴스 수를 즉시 변경 하지 않습니다. 즉각적인 변경을 위해 [수동 크기 조정을](manage-cluster-horizontal-scaling.md#manual-scale) 사용 하 여 권장 되는 인스턴스 수를 다시 설정한 후 최적화 된 자동 크기 조정을 사용 하도록 설정 합니다.

#### <a name="update-cache-policy-for-azure-data-explorer-tables"></a>Azure 데이터 탐색기 테이블에 대 한 업데이트 캐시 정책

다른 조회 기간 (시간 필터) 또는 더 큰 [캐시 정책이](kusto/management/cachepolicy.md)필요한 클러스터에 대 한 향상 된 성능 권장 사항은 **Azure 데이터 탐색기 테이블 캐시 기간 (정책) 검토를 참조** 하세요. 이 권장 사항은 지난 30 일 동안의 쿼리 조회 기간을 기반으로 합니다. 지난 30 일 동안 실행 된 대부분의 쿼리는 캐시에 없는 데이터에 액세스 하 여 쿼리 실행 시간을 늘릴 수 있습니다.  쿼리 비율에 따라 순서가 지정 된 캐시 외부 데이터에 액세스 하는 상위 10 개 테이블이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* [변경 수요에 맞게 Azure 데이터 탐색기의 클러스터 수평적 크기 조정 (규모 확장) 관리](manage-cluster-horizontal-scaling.md)
* [변경 수요에 맞게 Azure 데이터 탐색기에서 클러스터 수직 확장 (강화) 관리](manage-cluster-vertical-scaling.md)
