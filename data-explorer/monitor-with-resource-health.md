---
title: Resource Health를 사용 하 여 Azure 데이터 탐색기 모니터링
description: Azure Resource Health를 사용 하 여 Azure 데이터 탐색기 리소스를 모니터링 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/31/2020
ms.openlocfilehash: 5de30b4e6eb2ac9d01314482b693f6643075ca4a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872746"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Resource Health를 사용 하 여 Azure 데이터 탐색기 모니터링 (미리 보기)

Azure 데이터 탐색기 [Resource Health](/azure/service-health/resource-health-overview) 는 azure 데이터 탐색기 리소스의 상태를 알려 주며 문제 해결을 위한 조치 가능한 권장 사항을 제공 합니다. 리소스 상태는 1-2 분 마다 업데이트 되며 리소스의 현재 및 과거 상태를 보고 합니다. 

Resource Health는 다음과 같은 다양 한 상태 검사를 검토 하 여 Azure 데이터 탐색기 리소스의 상태를 확인 합니다.
* 리소스 가용성
* 쿼리 실패

## <a name="access-resource-health-reporting"></a>액세스 Resource Health 보고

1. [Azure Portal](https://portal.azure.com/)에서 서비스 목록에서 **모니터** 를 선택 합니다.
1. **Service Health**  >  **리소스 상태**를 선택 합니다.
1. **구독** 드롭다운에서 구독을 선택 합니다. **리소스 종류** 드롭다운에서 **Azure 데이터 탐색기**를 선택 합니다.
1. 결과 테이블에는 선택한 구독의 모든 리소스가 나열 됩니다. 각 리소스에는 리소스 상태를 나타내는 상태 아이콘이 있습니다.
1. 리소스 [상태](#resource-health-status) 및 권장 사항을 보려면 리소스를 선택 합니다.

    ![개요](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Resource Health 상태

리소스의 상태는 다음 상태 중 하나 (사용 가능, 사용 불가 및 알 수 없음)로 표시 됩니다.

### <a name="available"></a>사용 가능

**사용 가능** 상태는 Azure 데이터 탐색기 리소스가 정상이 고 문제가 없음을 나타냅니다.

![사용 가능](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Unavailable

**사용할 수 없음** 상태는 쿼리 및 수집에 사용할 수 없게 되는 Azure 데이터 탐색기 리소스에 지속적으로 문제가 있음을 나타냅니다. 예를 들어 Azure 데이터 탐색기 리소스의 노드가 예기치 않게 다시 부팅 되었을 수 있습니다. Azure 데이터 탐색기 리소스가 오랜 시간 동안이 상태를 유지 하는 경우 [지원]()담당자에 게 문의 하세요.

![Unavailable](media/monitor-with-resource-health/unavailable.png)

> [!TIP]
> [시스템 정보 명령을](kusto/management/systeminfo.md) 사용 하 여 문제의 원인을 찾을 수 있습니다.

### <a name="unknown"></a>Unknown

**알 수 없음** 상태는 **Resource Health** 이 Azure 데이터 탐색기 리소스에 대 한 정보를 10 분 넘게 수신 하지 못했음을 나타냅니다. 이 상태는 Azure 데이터 탐색기 리소스 상태를 명확 하 게 표시 하는 것이 아니라 문제 해결 프로세스의 중요 한 데이터 요소입니다. Azure 데이터 탐색기 클러스터가 예상 대로 작동 하는 경우 몇 분 내에 상태가 **사용 가능** 으로 변경 됩니다. **알 수 없는** 상태는 플랫폼의 이벤트가 리소스에 영향을 주는 것을 제안할 수 있습니다. 

> [!TIP]
> Azure 데이터 탐색기 클러스터 리소스 상태가 중지 됨 상태인 경우 **알** 수 없습니다.

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>기록 정보

**Resource Health** 창에서 **상태 기록**> 최대 4 주 동안의 리소스 상태 정보에 액세스 합니다. 이 창에 보고 된 상태 이벤트 문제에 대 한 추가 정보를 보려면 화살표를 선택 합니다. 

![기록](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>다음 단계

* [Resource Health 경고 구성](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [자습서: Azure 데이터 탐색기에서 모니터링 데이터 수집 및 쿼리](ingest-data-no-code.md)
* [메트릭을 사용 하 여 Azure 데이터 탐색기 성능, 상태 및 사용 현황 모니터링](using-metrics.md)
