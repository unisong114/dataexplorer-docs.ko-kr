---
title: Azure 데이터 탐색기의 수요에 맞게 클러스터 수직 확장(확장) 관리
description: 이 문서에서는 변화하는 수요에 따라 Azure Data Explorer 클러스터를 확장하고 축소하는 단계에 대해 설명합니다.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496187"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Azure 데이터 탐색기에서 클러스터 수직 확장(확장) 관리하여 변화하는 수요를 수용합니다.

클러스터 크기를 적절하게 조정하는 것은 Azure 데이터 탐색기의 성능에 중요합니다. 정적 클러스터 크기는 사용량 미달 또는 사용량 초과로 이어질 수 있으며, 둘 다 이상적인 경우는 아닙니다.

클러스터에 대한 수요를 절대 적인 정확도로 예측할 수 없으므로 더 나은 방법은 변화하는 수요에 따라 클러스터를 *확장하고* 용량 및 CPU 리소스를 추가 및 제거하는 것입니다. 

Azure 데이터 탐색기 클러스터를 확장하기 위한 두 가지 워크플로가 있습니다.

* [수평 크기 조정은](manage-cluster-horizontal-scaling.md)인/아웃이라고도 합니다.
* 수직 크기 조정이라고도 합니다.

이 문서에서는 수직 크기 조정 워크플로를 설명합니다.

## <a name="configure-vertical-scaling"></a>수직 크기 조정 구성

1. Azure 포털에서 Azure 데이터 탐색기 클러스터 리소스로 이동합니다. **설정에서**확장 을 **선택합니다.**

1. **확장** 창에는 클러스터에 사용할 수 있는 SCO 목록이 표시됩니다. 예를 들어 다음 그림에서는 4개의 SCO만 사용할 수 있습니다.

    ![강화](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU는 현재 SKU이거나 클러스터가 있는 리전에서 사용할 수 없기 때문에 비활성화됩니다.

1. SKU를 변경하려면 새 SKU를 선택하고 **을 클릭합니다.**

> [!NOTE]
> * 수직 크기 조정 프로세스는 몇 분 정도 걸릴 수 있으며 이 기간 동안 클러스터가 일시 중단됩니다. 
> * 축소하면 클러스터 성능에 해를 끼칠 수 있습니다.
> * 가격은 클러스터의 가상 컴퓨터 및 Azure 데이터 탐색기 서비스 비용의 추정치입니다. 기타 비용은 포함되어 있지 않습니다. 예상 요금은 Azure 데이터 탐색기 [비용 추정기](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) 페이지를 참조하고 전체 가격 정보는 Azure Data 탐색기 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/data-explorer/) 참조하세요.

이제 Azure 데이터 탐색기 클러스터에 대해 수직 크기 조정을 구성했습니다. 가로 배율 조정에 대한 다른 규칙을 추가합니다. 클러스터 크기 조정 문제에 대한 지원이 필요한 경우 Azure 포털에서 [지원 요청을 엽니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>다음 단계

* [클러스터 수평 확장을 관리하여](manage-cluster-horizontal-scaling.md) 지정한 메트릭을 기반으로 인스턴스 수를 동적으로 확장할 수 있습니다.

* 이 문서에 따라 리소스 사용량을 모니터링합니다: [메트릭을 사용하여 Azure 데이터 탐색기 성능, 상태 및 사용량 모니터링.](using-metrics.md)

