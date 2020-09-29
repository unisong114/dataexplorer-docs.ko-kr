---
title: 구체화 된 뷰-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구체화 된 보기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 77c86708a20349f5864bd10fa298719dce0fbab9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452802"
---
# <a name="materialized-views-preview"></a>구체화 된 뷰 (미리 보기)

[구체화 된 뷰](../../query/materialized-view-function.md) 는 원본 테이블에 대 한 *집계* 쿼리를 노출 합니다. 구체화 된 뷰는 항상 집계 쿼리 (항상 최신)의 최신 결과를 반환 합니다. [구체화 된 뷰를 쿼리](#materialized-views-queries) 하는 것은 각 쿼리를 수행 하는 원본 테이블에서 직접 집계를 실행 하는 것 보다 성능이 더 우수 합니다.

> [!NOTE]
> 구체화 된 뷰에는 몇 가지 [제한 사항이](materialized-view-create.md#limitations-on-creating-materialized-views)있으며 모든 시나리오에서 제대로 작동 하지 않을 수 있습니다. 기능을 사용 하기 전에 [성능 고려 사항을](#performance-considerations) 검토 합니다.

다음 명령을 사용 하 여 구체화 된 뷰를 관리 합니다.
* [.create materialized-view](materialized-view-create.md)
* [.alter materialized-view](materialized-view-alter.md)
* [.drop materialized-view](materialized-view-drop.md)
* [.disable | .enable materialized-view](materialized-view-enable-disable.md)
* [. 구체화 된 뷰 표시 명령](materialized-view-show-commands.md)

## <a name="why-use-materialized-views"></a>구체화 된 뷰를 사용 하는 이유

일반적으로 사용 되는 집계의 구체화 된 뷰에 대해 리소스 (데이터 저장소, 백그라운드 CPU 주기)를 투자 하 여 다음과 같은 이점을 얻을 수 있습니다.

* **성능 향상:** 구체화 된 뷰를 쿼리 하는 것은 일반적으로 동일한 집계 함수에 대 한 원본 테이블을 쿼리 하는 것 보다 효율적으로 수행 됩니다.

* **새로 고침:** 구체화 된 뷰 쿼리는 마지막으로 구체화가 수행 된 경우와 무관 하 게 최신 결과를 항상 반환 합니다. 이 쿼리는 뷰에서 구체화 된 부분을 원본 테이블의 레코드와 결합 합니다 .이는 아직 구체화 되지 않은 ( `delta` 파트) 항상 최신 결과를 제공 합니다.

* **비용 절감:** [구체화 된 뷰를 쿼리하면](#materialized-views-queries) 원본 테이블에 대 한 집계를 수행 하는 것 보다 클러스터의 리소스가 더 적게 소모 됩니다. 집계만 필요한 경우 원본 테이블의 보존 정책을 줄일 수 있습니다. 이 설정은 원본 테이블에 대 한 핫 캐시 비용을 줄입니다.

## <a name="materialized-views-use-cases"></a>구체화 된 뷰 사용 사례

구체화 된 뷰를 사용 하 여 해결할 수 있는 일반적인 시나리오는 다음과 같습니다.

* [Arg_max () (집계 함수)](../../query/arg-max-aggfunction.md)를 사용 하 여 엔터티 당 마지막 레코드를 쿼리 합니다.
* [Any () (집계 함수)](../../query/any-aggfunction.md)를 사용 하 여 테이블에서 중복 레코드를 제거 합니다.
* 원시 데이터에 대 한 주기적인 통계를 계산 하 여 데이터의 해상도를 줄입니다. 기간별로 다양 한 [집계 함수](materialized-view-create.md#supported-aggregation-functions) 를 사용 합니다.
    * 예를 들어를 사용 `T | summarize dcount(User) by bin(Timestamp, 1d)` 하 여 하루에 고유한 사용자의 최신 스냅숏을 유지 합니다.

## <a name="how-materialized-views-work"></a>구체화 된 뷰 작동 방법

구체화 된 뷰는 다음과 같은 두 가지 구성 요소로 구성 됩니다.

* *구체화* 된 부분-원본 테이블에서 집계 된 레코드를 포함 하는 Azure 데이터 탐색기 테이블로 이미 처리 되었습니다.  이 테이블은 항상 집계의 그룹별 단일 레코드를 조합 하 여 보유 합니다.
* *델타* -원본 테이블에서 아직 처리 되지 않은 새로 수집 레코드입니다.

구체화 된 뷰를 쿼리하면 구체화 된 파트가 델타 부분과 결합 되어 집계 쿼리의 최신 결과가 제공 됩니다. 오프 라인 구체화 프로세스는 새 레코드를 *델타* 에서 구체화 된 테이블로 수집 기존 레코드를 바꿉니다. 대체할 레코드를 포함 하는 익스텐트를 다시 작성 하 여 대체 작업을 수행 합니다. *델타* 의 레코드가 *구체화* 된 파트의 모든 데이터 분할 지속적으로 교차 하는 경우 각 구체화 주기는 전체 *구체화* 된 파트를 다시 작성 해야 하며 수집 속도를 유지 하지 않을 수 있습니다. 이 경우 뷰가 비정상 상태가 되 고 *델타* 는 지속적으로 증가 합니다.
[모니터링](#materialized-views-monitoring) 섹션에서는 이러한 상황을 해결 하는 방법을 설명 합니다.

## <a name="materialized-views-queries"></a>구체화 된 뷰 쿼리

구체화 된 뷰를 쿼리 하는 기본 방법은 테이블 참조 쿼리와 같이 이름을 기준으로 하는 것입니다. 구체화 된 뷰를 쿼리하면 뷰의 구체화 된 부분이 아직 구체화 되지 않은 원본 테이블의 레코드와 결합 됩니다. 구체화 된 뷰를 쿼리하면 원본 테이블에 수집 모든 레코드를 기반으로 하 여 항상 최신 결과가 반환 됩니다. 구체화 된 뷰 파트의 분석에 대 한 자세한 내용은 구체화 된 [뷰 작동 방식](#how-materialized-views-work)을 참조 하세요. 

뷰를 쿼리 하는 또 다른 방법은 [materialized_view () 함수](../../query/materialized-view-function.md)를 사용 하는 것입니다. 이 옵션은 사용자가 허용할 최대 대기 시간을 지정 하는 동시에 뷰의 구체화 된 부분만 쿼리 하는 것을 지원 합니다. 이 옵션은 최신 레코드를 반환 하는 것을 보장 하지는 않지만 항상 전체 뷰를 쿼리 하는 것 보다 성능이 더 우수 합니다. 이 함수는 원격 분석 대시보드의 경우와 같이 성능에 대 한 몇 가지 최신 유효성을 희생 하려는 시나리오에 유용 합니다.

뷰는 클러스터 간 또는 데이터베이스 간 쿼리에 참여할 수 있지만 와일드 카드 공용 구조체 또는 검색에는 포함 되지 않습니다.

### <a name="examples"></a>예

1. 전체 뷰를 쿼리 합니다. 원본 테이블의 최신 레코드는 다음과 같습니다.
    
    <!-- csl -->
    ```kusto
    ViewName
    ```

1. 마지막으로 구체화 된 시점에 관계 없이 뷰의 구체화 된 부분만 쿼리 합니다. 

    <!-- csl -->
    ```kusto
    materialized_view("ViewName")
    ```
  
## <a name="performance-considerations"></a>성능 고려 사항

구체화 된 뷰 상태에 영향을 줄 수 있는 주요 참여자는 다음과 같습니다.

* **클러스터 리소스:** 클러스터에서 실행 되는 다른 프로세스와 마찬가지로 구체화 된 뷰는 클러스터에서 리소스 (CPU, 메모리)를 사용 합니다. 클러스터가 오버 로드 된 경우 구체화 된 뷰를 추가 하면 클러스터의 성능이 저하 될 수 있습니다. [클러스터 상태 메트릭을](../../../using-metrics.md#cluster-metrics)사용 하 여 클러스터의 상태를 모니터링 합니다. [최적화 된 자동 크기 조정](../../../manage-cluster-horizontal-scaling.md#optimized-autoscale) 에서는 현재 자동 크기 조정 규칙의 일부로 고려 하 여 구체화 된 뷰 상태를 사용 하지 않습니다.

* **구체화 된 데이터와 겹칩니다.** 구체화 중에는 마지막 구체화 (델타)가 처리 되 고 뷰로 구체화 된 후 모든 새 레코드가 원본 테이블로 수집 됩니다. 새 레코드와 이미 구체화 된 레코드 간의 교차 수준이 높을수록 구체화 된 뷰의 성능은 저하 됩니다. 구체화 된 뷰는 업데이트 되는 레코드 수 (예: `arg_max` 보기)가 원본 테이블의 작은 하위 집합인 경우에 가장 잘 작동 합니다. 모든 구체화 주기에서 구체화 된 뷰 레코드를 모두 또는 대부분 업데이트 해야 하는 경우 구체화 된 뷰가 제대로 수행 되지 않습니다. [익스텐트 다시 작성 메트릭을](../../../using-metrics.md#materialized-view-metrics) 사용 하 여이 상황을 확인 합니다.

* 수집 **율:** 구체화 된 뷰의 원본 테이블에는 데이터 볼륨 또는 수집 율에 대 한 하드 코드 된 제한이 없습니다. 그러나 구체화 된 뷰에 대해 권장 되는 수집 률은 1 ~ 2GB/초가 아닙니다. 수집 속도가 더 높을 수도 있습니다. 성능은 클러스터 크기, 사용 가능한 리소스 및 기존 데이터와의 교차 양에 따라 달라 집니다.

* **클러스터의 구체화 된 뷰 수:** 위의 고려 사항은 클러스터에 정의 된 각각의 구체화 된 뷰에 적용 됩니다. 각 보기는 자체 리소스를 사용 하며, 많은 보기가 사용 가능한 리소스에서 서로 경쟁 합니다. 클러스터의 구체화 된 뷰 수에 대 한 하드 코드 된 제한은 없습니다. 그러나 일반적으로 클러스터에는 구체화 된 뷰를 10 개 이하로 포함 하는 것이 좋습니다. 구체화 된 뷰가 두 개 이상 클러스터에 정의 되어 있으면 [용량 정책이](../capacitypolicy.md#materialized-views-capacity-policy) 조정 될 수 있습니다.

* **구체화 된 뷰 정의**: 최상의 쿼리 성능을 위해 구체화 된 뷰 정의를 쿼리 모범 사례에 따라 정의 해야 합니다. 자세한 내용은 [명령 성능 팁 만들기](materialized-view-create.md#performance-tips)를 참조 하세요.

## <a name="materialized-views-policies"></a>구체화 된 뷰 정책

Azure 데이터 탐색기 테이블과 같이 구체화 된 뷰의 [보존 정책](../retentionpolicy.md) 및 [캐싱 정책을](../cachepolicy.md) 정의할 수 있습니다.

구체화 된 뷰는 기본적으로 데이터베이스 보존 및 캐싱 정책을 파생 합니다. [보존 정책 제어 명령](../retention-policy.md) 또는 [캐싱 정책 제어 명령을](../cache-policy.md)사용 하 여 정책을 변경할 수 있습니다.
   
   * 구체화 된 뷰의 보존 정책은 원본 테이블의 보존 정책과 관련이 없습니다.
   * 원본 테이블 레코드를 사용 하지 않는 경우 원본 테이블의 보존 정책을 최소한으로 삭제할 수 있습니다. 구체화 된 뷰는 보기에 설정 된 보존 정책에 따라 데이터를 계속 저장 합니다. 
   * 구체화 된 뷰는 미리 보기 모드에 있지만 최소 7 일 이상 및 복구 가능을 true로 설정 하는 것이 좋습니다. 이 설정은 오류에 대 한 빠른 복구와 진단 목적으로 허용 됩니다.
    
> [!NOTE]
> 원본 테이블에 대 한 보존 정책은 현재 지원 되지 않습니다.

## <a name="materialized-views-monitoring"></a>구체화 된 뷰 모니터링

다음과 같은 방법으로 구체화 된 뷰의 상태를 모니터링 합니다.

* Azure Portal에서 [구체화 된 뷰 메트릭을](../../../using-metrics.md#materialized-view-metrics) 모니터링 합니다.
* 구체화 된 `IsHealthy` [뷰 표시](materialized-view-show-commands.md#show-materialized-view)에서 반환 된 속성을 모니터링 합니다.
* [구체화 된 뷰 오류 표시](materialized-view-show-commands.md#show-materialized-view-failures)를 사용 하 여 오류를 확인 합니다.

> [!NOTE]
> 구체화는 일정 한 오류가 있는 경우에도 데이터를 건너뛰고 있지 않습니다. 뷰는 항상 원본 테이블의 모든 레코드를 기반으로 쿼리의 최신 스냅숏을 반환 하도록 보장 됩니다. 상수 오류가 발생 하면 쿼리 성능이 크게 저하 되지만 뷰 쿼리에서 잘못 된 결과가 발생 하지는 않습니다.

### <a name="track-resource-consumption"></a>리소스 소비 추적

**구체화 된 뷰 리소스 사용:** 구체화 된 뷰 구체화 프로세스에서 사용 하는 리소스는 [. 명령 표시 및 쿼리](../commands-and-queries.md#show-commands-and-queries) 명령을 사용 하 여 추적할 수 있습니다. 다음을 사용 하 여 특정 뷰의 레코드를 필터링 합니다 (replace `DatabaseName` 및 `ViewName` ).

<!-- csl -->
```
.show commands-and-queries 
| where Database  == "DatabaseName" and ClientActivityId startswith "DN.MaterializedViews;ViewName;"
```

### <a name="troubleshooting-unhealthy-materialized-views"></a>비정상 구체화 뷰 문제 해결

`MaterializedViewHealth`메트릭은 구체화 된 뷰가 정상 상태 인지 여부를 나타냅니다. 구체화 된 뷰는 다음과 같은 이유 중 하나 또는 모두에 대해 비정상 상태가 될 수 있습니다.
* 구체화 프로세스가 실패 합니다.
* 클러스터에는 들어오는 모든 데이터를 실시간으로 구체화할 수 있는 충분 한 용량이 없습니다. 실행 하는 동안 오류가 발생 하지 않습니다. 그러나 뷰는 지연 되 고 수집 속도를 유지할 수 없으므로 비정상 상태입니다.

구체화 된 뷰가 비정상 상태가 되기 전에 메트릭에 의해 기록 된 해당 나이가 `MaterializedViewAgeMinutes` 점차 증가 합니다.

### <a name="troubleshooting-examples"></a>문제 해결 예제

다음 예에서는 비정상 뷰를 진단 하 고 수정 하는 데 도움이 될 수 있습니다.

* **실패:** 원본 테이블이 변경 되거나 삭제 되었거나, 뷰가로 설정 `autoUpdateSchema` 되지 않았거나, 원본 테이블의 변경 내용이 자동 업데이트에 대해 지원 되지 않습니다. <br>
   **진단:**  `MaterializedViewResult`메트릭이 발생 하 고 `Result` 차원이로 설정 됩니다 `SourceTableSchemaChange` / `SourceTableNotFound` .

* **실패:** 클러스터 리소스가 부족 하 여 구체화 프로세스가 실패 하 고 쿼리 한도가 적중 됩니다. <br>
  **진단:** `MaterializedViewResult` 메트릭 `Result` 차원이로 설정 됩니다 `InsufficientResources` . Azure 데이터 탐색기이 상태에서 자동으로 복구를 시도 하므로이 오류는 일시적일 수 있습니다. 그러나 보기가 비정상 상태이 고이 오류가 지속적으로 발생 하는 경우 현재 클러스터의 구성이 수집 속도를 유지할 수 없으며 클러스터를 확장 하거나 축소 해야 할 수 있습니다.

* **실패:** 다른 (알 수 없는) 이유로 구체화 프로세스가 실패 합니다. <br> 
   **진단**: `MaterializedViewResult` 메트릭이 됩니다 `Result` `UnknownError` . 이 오류가 자주 발생 하는 경우 Azure 데이터 탐색기 팀에 대 한 지원 티켓을 열어 추가로 조사 하세요.

구체화 오류가 없는 경우에는를 `MaterializedViewResult` 사용 하 여 성공적으로 실행 될 때마다 메트릭이 발생 `Result` = `Success` 합니다. 구체화 된 뷰는 성공한 실행에도 불구 하 고 지연 ( `Age` 가 임계값을 초과 하는 경우)에도 비정상 상태가 될 수 있습니다. 이러한 상황은 다음과 같은 경우에 발생할 수 있습니다.
   * 각 구체화 주기에서 다시 작성할 익스텐트가 너무 많기 때문에 구체화가 느립니다. 익스텐트 재작성이 보기의 성능에 영향을 주는 이유에 대 한 자세한 내용은 [구체화 된 보기의 작동 방식](#how-materialized-views-work)을 참조 하세요. 
   * 각 구체화 주기를 뷰에서 익스텐트의 100%까지 다시 빌드해야 하는 경우 뷰가 유지 되지 않고 비정상 상태가 될 수 있습니다. 각 주기에서 다시 작성 된 익스텐트 수는 메트릭에 제공 됩니다 `MaterializedViewExtentsRebuild` . [구체화 된 뷰 용량 정책](../capacitypolicy.md#materialized-views-capacity-policy) 에서 다시 작성 된 익스텐트를 늘려도이 경우 도움이 될 수도 있습니다. 
   * 클러스터에 구체화 된 뷰가 추가로 있으며 클러스터에 모든 보기를 실행 하는 데 충분 한 용량이 없습니다. 동시에 실행 되는 구체화 된 뷰의 수에 대 한 기본 설정을 변경 하려면 [구체화 된 뷰 용량 정책](../capacitypolicy.md#materialized-views-capacity-policy) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [. 구체화 된 뷰 만들기](materialized-view-create.md)
* [.alter materialized-view](materialized-view-alter.md)
* [구체화 된 뷰 명령 표시](materialized-view-show-commands.md)
