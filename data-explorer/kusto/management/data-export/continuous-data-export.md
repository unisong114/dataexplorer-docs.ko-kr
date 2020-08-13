---
title: 연속 데이터 내보내기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 지속적인 데이터 내보내기를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: fab1f41fc4b72b497900276d33beb1b89820c02c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201636"
---
# <a name="continuous-data-export-overview"></a>연속 데이터 내보내기 개요

이 문서에서는 정기적으로 쿼리를 실행 하 여 Kusto에서 [외부 테이블로](../externaltables.md) 데이터를 연속으로 내보내는 방법을 설명 합니다. 결과는 Azure Blob Storage와 같은 대상 및 내보낸 데이터의 스키마를 정의 하는 외부 테이블에 저장 됩니다. 이 프로세스는 일부 [예외를 제외](#exactly-once-export)하 고 모든 레코드를 "정확히 한 번" 내보낼 수 있도록 보장 합니다. 

연속 데이터 내보내기를 사용 하도록 설정 하려면 [외부 테이블을 만든](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table) 다음 외부 테이블을 가리키는 [연속 내보내기 정의를 만듭니다](create-alter-continuous.md) . 

> [!NOTE]
> 모든 연속 내보내기 명령에는 [데이터베이스 관리자 권한이](../access-control/role-based-authorization.md)필요 합니다.

## <a name="continuous-export-guidelines"></a>연속 내보내기 지침

* **출력 스키마**:
  * 내보내기 쿼리의 출력 스키마는 내보낼 외부 테이블의 스키마와 일치 해야 합니다. 
* **빈도**:
  * 연속 내보내기는 속성에서 구성 된 기간에 따라 실행 됩니다 `intervalBetweenRuns` . 이 간격에 권장 되는 값은 허용 되는 대기 시간에 따라 몇 분 이상입니다. 수집 속도가 높으면 시간 간격은 1 분 정도로 낮을 수 있습니다.
* **배포**:
  * 연속 내보내기의 기본 배포는 `per_node` 모든 노드가 동시에 내보내는입니다. 
  * 이 설정은 연속 내보내기 만들기 명령의 속성에서 재정의할 수 있습니다. `per_shard`배포를 사용 하 여 동시성을 늘립니다.
    > [!NOTE]
    > 이 배포는 저장소 계정에 대 한 부하를 증가 시키고 제한 한도에 도달할 가능성이 있습니다. 
  * `single`(또는 `distributed` = `false` )를 사용 하 여 배포를 모두 사용 하지 않도록 설정 합니다. 이렇게 설정 하면 연속 내보내기 프로세스가 느려지고 각 연속 내보내기 반복에서 생성 되는 파일의 수에 영향을 줄 수 있습니다. 
* **파일 수**:
  * 각 연속 내보내기 반복에서 내보낸 파일 수는 외부 테이블이 분할 되는 방식에 따라 달라 집니다. 자세한 내용은 [외부 테이블로 내보내기 명령을](export-data-to-an-external-table.md#number-of-files)참조 하세요. 각 연속 내보내기 반복은 항상 새 파일에 쓰고 기존 파일에 추가 하지 않습니다. 결과적으로, 내보낸 파일의 수도 연속 내보내기가 실행 되는 빈도에 따라 달라 집니다. Frequency 매개 변수는 `intervalBetweenRuns` 입니다.
* **위치**:
  * 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 저장소 계정은 동일한 Azure 지역에서 공동 배치 되어야 합니다.
  * 내보낸 데이터 볼륨이 크면 저장소 제한을 방지 하기 위해 외부 테이블에 대해 여러 저장소 계정을 구성 하는 것이 좋습니다. [저장소로 데이터 내보내기를](export-data-to-storage.md#known-issues)참조 하세요.

## <a name="exactly-once-export"></a>정확히 한 번 내보내기

"정확히 한 번" 내보내기를 보장 하기 위해 연속 내보내기는 [데이터베이스 커서](../databasecursor.md)를 사용 합니다. [IngestionTime 정책은](../ingestiontime-policy.md) 내보내기에서 "정확히 한 번" 처리 되어야 하는 쿼리에서 참조 되는 모든 테이블에 대해 사용 하도록 설정 해야 합니다. 정책은 새로 만든 모든 테이블에서 기본적으로 사용 하도록 설정 됩니다.

"정확히 한 번" 내보내기는 [내보낸 아티팩트 표시 명령](show-continuous-artifacts.md)에 보고 된 파일에 대해서만 보장 됩니다. 연속 내보내기는 각 레코드가 외부 테이블에 한 번만 기록 되는 것을 보장 하지 않습니다. 내보내기가 시작 되 고 일부 아티팩트가 이미 외부 테이블에 기록 된 후에 오류가 발생 하면 외부 테이블에 중복 항목이 포함 될 수 있습니다. 쓰기 작업이 완료 되기 전에 중단 된 경우에는 외부 테이블에 손상 된 파일이 있을 수 있습니다. 이러한 경우 아티팩트는 외부 테이블에서 삭제 되지 않지만 [내보낸 아티팩트 표시 명령](show-continuous-artifacts.md)에는 보고 되지 않습니다. 을 사용 하 여 내보낸 파일을 사용 하면 `show exported artifacts command` 중복이 보장 되지 않으며 손상 되지 않습니다.

## <a name="export-to-fact-and-dimension-tables"></a>팩트 및 차원 테이블로 내보내기

기본적으로 내보내기 쿼리에서 참조 되는 모든 테이블은 [팩트 테이블로](../../concepts/fact-and-dimension-tables.md)간주 됩니다. 따라서 데이터베이스 커서로 범위가 한정 됩니다. 구문은 범위가 지정 된 테이블 (팩트) 및 범위가 지정 되지 않은 테이블 (차원)을 명시적으로 선언 합니다. 자세한 내용은 `over` [create 명령의](create-alter-continuous.md) 매개 변수를 참조 하세요.

내보내기 쿼리는 이전 내보내기 실행 이후 조인 된 레코드만 포함 합니다. 내보내기 쿼리에는 차원 테이블의 모든 레코드가 모든 내보내기 쿼리에 포함 되는 [차원 테이블이](../../concepts/fact-and-dimension-tables.md) 포함 될 수 있습니다. 연속-내보내기에서 팩트 테이블과 차원 테이블 간의 조인을 사용 하는 경우 팩트 테이블의 레코드는 한 번만 처리 된다는 점에 유의 하세요. 일부 키에 대해 차원 테이블의 레코드를 누락 하는 동안 내보내기가 실행 되 면 해당 키에 대 한 레코드가 누락 되거나 내보낸 파일의 차원 열에 대해 null 값이 포함 됩니다. 누락 또는 null 레코드 반환은 쿼리에서 내부 조인 또는 외부 조인을 사용 하는지에 따라 달라 집니다. `forcedLatency`연속 내보내기 정의의 속성은 일치 하는 레코드에 대해 팩트 및 차원 테이블을 동시에 수집 하는 경우에 유용할 수 있습니다.

> [!NOTE]
> 차원 테이블의 연속 내보내기는 지원 되지 않습니다. 내보내기 쿼리는 적어도 하나의 팩트 테이블을 포함 해야 합니다.

## <a name="exporting-historical-data"></a>기록 데이터 내보내기

연속 내보내기는 생성 시점 에서만 데이터 내보내기를 시작 합니다. 연속 하지 않는 [내보내기 명령을](export-data-to-an-external-table.md)사용 하 여이 시간 전에 수집 레코드를 별도로 내보내야 합니다. 

기록 데이터가 너무 커서 단일 내보내기 명령으로 내보낼 수 없습니다. 필요한 경우 쿼리를 몇 개의 작은 일괄 처리로 분할 합니다. 

연속 내보내기로 내보낸 데이터에 중복 되지 않도록 하려면 `StartCursor` [연속 내보내기 표시 명령](show-continuous-export.md) 에 의해 반환 된를 사용 하 고 내보내기는 `where cursor_before_or_at` 커서 값을 기록 합니다. 예를 들어:

```kusto
.show continuous-export MyExport | project StartCursor
```

| StartCursor        |
|--------------------|
| 636751928823156645 |

다음이 이어집니다. 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```

## <a name="resource-consumption"></a>리소스 사용

* 연속 내보내기의 영향은 연속 내보내기가 실행 되는 쿼리에 따라 달라 집니다. CPU, 메모리 등의 대부분의 리소스는 쿼리 실행에 사용 됩니다. 
* 동시에 실행할 수 있는 내보내기 작업 수는 클러스터의 데이터 내보내기 용량에 의해 제한 됩니다. 자세한 내용은 [제한](../../management/capacitypolicy.md#throttling)을 참조 하세요. 클러스터에 모든 연속 내보내기를 처리할 충분 한 용량이 없는 경우 일부는 지연 뒤에 시작 됩니다.
* [명령 및 쿼리 표시 명령을](../commands-and-queries.md) 사용 하 여 리소스 소비를 예측할 수 있습니다. 
  * `| where ClientActivityId startswith "RunContinuousExports"`연속 내보내기와 연결 된 명령 및 쿼리를 보려면를 필터링 합니다.

## <a name="limitations"></a>제한 사항

* 연속 내보내기는 스트리밍 수집을 사용 하는 데이터 수집 작동 하지 않습니다. 
* [행 수준 보안 정책이](../../management/rowlevelsecuritypolicy.md) 설정 된 테이블에서는 연속 내보내기를 구성할 수 없습니다.
* 연속 내보내기는 `impersonate` [연결 문자열](../../api/connection-strings/storage.md)에 있는 외부 테이블에 대해서는 지원 되지 않습니다.
* 연속 내보내기는 데이터베이스 간 및 클러스터 간 호출을 지원 하지 않습니다.
* 연속 내보내기는 Azure 데이터 탐색기에서 지속적으로 데이터를 스트리밍하는 데 적합 하지 않습니다. 연속 내보내기는 모든 노드가 동시에 내보내는 분산 모드에서 실행 됩니다. 각 실행에서 쿼리 하는 데이터 범위가 작은 경우 연속 내보내기의 출력은 많은 작은 아티팩트가 됩니다. 아티팩트의 수는 클러스터의 노드 수에 따라 달라 집니다.
* 연속 내보내기에 사용 되는 아티팩트가 Event Grid 알림을 트리거하기 위한 것 이라면 [Event Grid 설명서의 알려진 문제 섹션](../../../ingest-data-event-grid-overview.md#known-event-grid-issues)을 참조 하세요.
 