---
title: Azure 데이터 탐색기에서 중복 데이터 처리
description: 이 항목에서는 Azure 데이터 탐색기를 사용 하는 경우 중복 데이터를 처리 하는 다양 한 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/19/2018
ms.openlocfilehash: ef71c978681c9e355e82791d4df3ad7da8db66f7
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874701"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 중복 데이터 처리

클라우드로 데이터를 전송하는 디바이스는 데이터의 로컬 캐시를 유지 관리합니다. 데이터 크기에 따라 로컬 캐시는 데이터를 수일 동안 저장하거나 수 개월 동안 저장할 수도 있습니다. 디바이스 오작동으로 인해 캐시된 데이터가 다시 전송되어 분석 데이터베이스에 데이터 중복이 발생하지 않도록 분석 데이터를 보호하는 것이 좋습니다. 이 항목에서는 이러한 종류의 시나리오에서 중복 데이터를 처리하는 방법에 대한 모범 사례를 간략하게 설명합니다.

데이터 중복에 가장 효과적인 해결책은 중복을 방지하는 것입니다. 가능한 경우 데이터 파이프라인의 초기 단계에서 문제를 해결하여 데이터 파이프라인 내 데이터 이동과 관련한 비용을 절감하고 시스템에 수집된 중복 데이터를 처리하는 데 리소스를 소비하지 않도록 할 수 있습니다. 그러나 원본 시스템을 수정할 수 없는 경우 다양한 방법으로 문제를 해결할 수 있습니다.

## <a name="understand-the-impact-of-duplicate-data"></a>중복 데이터의 영향 파악

중복 데이터의 비율을 모니터링하세요. 중복 데이터의 비율을 확인했으면 문제의 범위와 비즈니스에 미치는 영향을 분석하고 적절한 해결 방법을 선택할 수 있습니다.

중복 레코드의 비율을 파악하기 위한 샘플 쿼리:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>중복 데이터 처리 방법

### <a name="solution-1-dont-remove-duplicate-data"></a>솔루션 #1: 중복 데이터를 제거 하지 않음

비즈니스 요구 사항 및 중복 데이터 허용치를 파악하세요. 일부 데이터 세트는 일정 비율의 중복 데이터를 사용하여 관리할 수 있습니다. 중복 데이터가 큰 영향을 주지 않는다면 무시해도 됩니다. 중복 데이터를 제거하지 않을 경우 수집 프로세스나 쿼리 성능에 추가적인 부담을 주지 않는다는 장점이 있습니다.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>솔루션 #2: 쿼리 중 중복 행 처리

또 다른 옵션은 쿼리 중 데이터의 중복 행을 필터링하는 것입니다. [`arg_max()`](kusto/query/arg-max-aggfunction.md)집계 함수를 사용 하 여 중복 레코드를 필터링 하 고 타임 스탬프 또는 다른 열을 기반으로 마지막 레코드를 반환할 수 있습니다. 이 방법을 사용할 경우 쿼리 중에 중복 제거가 수행되므로 수집 시간이 단축된다는 장점이 있습니다. 또한 중복 항목을 포함한 모든 레코드를 감사 및 문제 해결에 사용할 수 있습니다. `arg_max` 함수 사용 시 데이터가 쿼리될 때마다 쿼리 시간과 CPU 로드가 늘어난다는 단점이 있습니다. 쿼리 중인 데이터 양에 따라 이 해결 방법이 효과가 없거나 메모리를 소모할 수 있으며, 이 경우 다른 옵션으로 전환해야 합니다.

다음 예에서는 수집된 마지막 레코드에서 고유한 레코드를 결정하는 열 집합을 쿼리합니다.

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

또한 테이블을 직접 쿼리하는 대신 함수 내부에 이 쿼리를 배치할 수도 있습니다.

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>솔루션 #3: 수집 프로세스 중 중복 항목 필터링

또 다른 해결 방법으로는 수집 프로세스 중에 중복 항목을 필터링하는 방법이 있습니다. 시스템은 Kusto 테이블에 데이터가 수집되는 동안 중복 데이터를 무시합니다. 데이터는 준비 테이블에 수집되고 중복 행 제거 과정을 거친 후 다른 테이블에 복사됩니다. 이 해결 방법은 쿼리 성능이 이전 해결 방법보다 크게 향상된다는 장점이 있습니다. 단점으로는 수집 시간 증가 및 데이터 스토리지 비용 추가 등이 있습니다. Additionaly이 솔루션은 중복가 동시에 수집 않는 경우에만 작동 합니다. 중복 레코드를 포함 하는 동시 ingestions이 여러 개 있는 경우 중복 제거 프로세스에서 테이블에 있는 기존의 일치 하는 레코드를 찾지 못하므로 모든가 수집 수 있습니다.    

다음 예에서는 이 방법을 보여줍니다.

1. 동일한 스키마를 사용하여 다른 테이블 만들기:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. 이전에 수집된 레코드와 새 레코드의 조인을 방지하여 중복 레코드를 필터링하는 함수를 만듭니다.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > 조인은 CPU 바인딩 작업이며 시스템에 로드를 추가합니다.

1. `DeviceEventsUnique` 테이블에 [업데이트 정책](kusto/management/update-policy.md)을 설정합니다. `DeviceEventsAll` 테이블에 새 데이터가 들어오면 업데이트 정책이 활성화됩니다. Kusto 엔진은 새 [익스텐트](kusto/management/extents-overview.md)가 생성되면 이 함수를 자동으로 실행합니다. 처리 범위는 새로 생성된 데이터로 지정됩니다. 다음 명령은 원본 테이블(`DeviceEventsAll`), 대상 테이블(`DeviceEventsUnique`), `RemoveDuplicatesDeviceEvents` 함수를 연결하여 업데이트 정책을 만듭니다.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > 업데이트 정책은 수집 기간을 늘립니다. 수집 중에 데이터가 필터링된 후 두 번 수집되기 때문입니다(`DeviceEventsAll` 테이블에 한 번, `DeviceEventsUnique` 테이블에 한 번).

1. (선택 사항) 데이터 복사본을 저장하지 않도록 하려면 `DeviceEventsAll` 테이블에서 데이터 보존 기간을 짧게 설정합니다. 문제 해결을 위해 데이터를 보존할 기간과 데이터 양에 따라 일수를 선택합니다. 보존 기간을 `0d`일로 설정하면 데이터가 스토리지에 업로드되지 않으므로 COGS를 절약하고 성능을 개선할 수 있습니다.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>요약

데이터 중복은 여러 가지 방법으로 처리할 수 있습니다. 비즈니스에 적절한 방법을 결정하려면 가격 및 성능을 고려하여 옵션을 신중하게 평가하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)
