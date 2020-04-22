---
title: 리소스 상태를 사용하여 Azure 데이터 탐색기 모니터링
description: Azure 리소스 상태를 사용하여 Azure 데이터 탐색기 리소스를 모니터링합니다.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495602"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>리소스 상태(미리 보기)를 사용하여 Azure 데이터 탐색기 모니터링

Azure 데이터 탐색기용 [리소스 상태는](/azure/service-health/resource-health-overview) Azure Data Explorer 리소스의 상태를 알려주고 문제 해결을 위한 실행 가능한 권장 사항을 제공합니다. 리소스 상태는 1~2분마다 업데이트되며 리소스의 현재 및 과거 상태를 보고합니다. 

리소스 상태는 다음과 같은 다양한 상태 확인을 검사하여 Azure Data Explorer 리소스의 상태를 결정합니다.
* 리소스 가용성
* 쿼리 실패

## <a name="access-resource-health-reporting"></a>리소스 상태 보고 액세스

1. Azure [포털에서](https://portal.azure.com/)서비스 목록에서 **모니터를** 선택합니다.
1. **서비스 상태** > **리소스 상태를**선택합니다.
1. **구독** 드롭다운에서 구독을 선택합니다. 리소스 **유형** 드롭다운에서 **Azure 데이터 탐색기를**선택합니다.
1. 결과 테이블에는 선택한 구독의 모든 리소스가 나열됩니다. 각 리소스에는 리소스 상태를 나타내는 상태 아이콘이 있습니다.
1. 리소스상태를 확인하여 [리소스 상태](#resource-health-status) 및 권장 사항을 확인합니다.

    ![개요](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Resource Health 상태

리소스의 상태는 다음 상태 중 하나, 사용 가능 상태, 사용 가능 없음 및 알 수 없음 중 하나로 표시됩니다.

### <a name="available"></a>사용 가능

**사용 가능한** 상태상태는 Azure Data Explorer 리소스가 정상상태이며 문제가 없음을 나타냅니다.

![사용 가능](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>사용할 수 없음

사용할 수 **없는** 상태는 Azure Data Explorer 리소스에 쿼리 및 수집에 사용할 수 없는 문제가 있음을 나타냅니다. 예를 들어 Azure Data Explorer 리소스의 노드가 예기치 않게 재부팅되었을 수 있습니다. Azure Data Explorer 리소스가 오랜 기간 동안 이 상태로 유지되면 [지원]()팀에 문의하십시오.

![사용할 수 없음](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>알 수 없음

알 수 **없는** 상태는 **리소스 상태가** 이 Azure 데이터 탐색기 리소스에 대한 정보를 10분 이상 받지 못했음을 나타냅니다. 이 상태는 Azure Data Explorer 리소스 상태에 대한 확실한 표시는 아니지만 문제 해결 프로세스에서 중요한 데이터 지점입니다. Azure Data Explorer 클러스터가 예상대로 작동하는 경우 몇 분 내에 상태가 **사용 가능으로** 변경됩니다. **알 수 없는** 상태 는 플랫폼의 이벤트가 리소스에 영향을 미치고 있음을 나타낼 수 있습니다. 

> [!TIP]
> Azure 데이터 탐색기 클러스터 리소스 상태는 중지된 상태인 경우 **알 수 없습니다.**

![알 수 없음](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>기록 정보

**리소스 상태** 창 > **상태 기록에서**최대 4주 간의 리소스 상태 상태 정보에 액세스할 수 있습니다. 이 창에 보고된 상태 이벤트 문제에 대한 자세한 내용은 화살표를 선택합니다. 

![기록](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>다음 단계

* [리소스 상태 경고 구성](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [자습서: Azure 데이터 탐색기에서 데이터 수집 및 쿼리 모니터링](ingest-data-no-code.md)
* [메트릭을 사용하여 Azure 데이터 탐색기 성능, 상태 및 사용량 모니터링](using-metrics.md)