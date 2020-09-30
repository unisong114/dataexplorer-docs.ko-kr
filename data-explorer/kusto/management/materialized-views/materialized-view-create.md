---
title: 구체화 된 뷰 만들기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 구체화 된 뷰를 만드는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: f67b2d61cfed297886447a97dd178dfb578a2c68
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572146"
---
# <a name="create-materialized-view"></a>.create materialized-view

[구체화 된 뷰](materialized-view-overview.md) 는 단일 요약 문을 나타내는 원본 테이블에 대 한 집계 쿼리입니다.

다음 두 가지 방법으로 명령에서 *백필* 옵션으로 표시 되는 구체화 된 뷰를 만들 수 있습니다.

 * **원본 테이블의 기존 레코드를 기반으로 하 여 만들기:** 
      * 생성은 원본 테이블의 레코드 수에 따라 완료 하는 데 시간이 오래 걸릴 수 있습니다. 뷰는 완료 될 때까지 쿼리에 사용할 수 없습니다.
      * 이 옵션을 사용 하는 경우 create 명령은 이어야 `async` 하며, 실행은 [. 작업 표시](../operations.md#show-operations) 명령을 사용 하 여 모니터링할 수 있습니다.

    * [. 작업 취소](#cancel-materialized-view-creation) 명령을 사용 하 여 백필 프로세스를 취소할 수 있습니다.

      > [!IMPORTANT]
      > * 콜드 캐시의 데이터에는 백필 옵션을 사용할 수 없습니다. 필요한 경우 뷰를 만들기 위해 핫 캐시 기간을 늘립니다. 이 경우 확장 해야 할 수 있습니다.    
      > * 대량 원본 테이블의 경우 백필 옵션을 사용 하면 완료 하는 데 시간이 오래 걸릴 수 있습니다. 이 프로세스가 실행 되는 동안 실패 하면 transiently가 자동으로 다시 시도 되지 않고 create 명령을 다시 실행 해야 합니다.
    
* **이제 다음에서 구체화 된 뷰를 만듭니다.** 
    * 구체화 된 뷰는 빈 상태로 생성 되며 뷰를 만든 후에만 수집 레코드를 포함 합니다. 이러한 종류의 생성은 즉시 반환 되 고, 필요 하지 않으며 `async` , 뷰를 쿼리에 즉시 사용할 수 있습니다.

만들기 작업을 수행 하려면 [데이터베이스 관리자](../access-control/role-based-authorization.md) 권한이 필요 합니다. 구체화 된 뷰의 작성자가 관리자가 됩니다.

## <a name="syntax"></a>구문

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table` *Sourcetablename* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*쿼리입니다*<br>`}`

## <a name="arguments"></a>인수

|인수|Type|Description
|----------------|-------|---|
|ViewName|String|구체화 된 뷰 이름입니다. 뷰 이름은 같은 데이터베이스에 있는 테이블 또는 함수 이름과 충돌 하지 않으며 [식별자 명명 규칙](../../query/schema-entities/entity-names.md#identifier-naming-rules)을 따라야 합니다. |
|(|String|뷰가 정의 된 원본 테이블의 이름입니다.|
|쿼리|String|구체화 된 뷰 쿼리입니다. 자세한 내용은 [쿼리](#query-argument)를 참조 하세요.|

### <a name="query-argument"></a>쿼리 인수

구체화 된 뷰 인수에 사용 되는 쿼리는 다음 규칙에 의해 제한 됩니다.

* 쿼리 인수는 구체화 된 뷰의 원본인 단일 팩트 테이블을 참조 하 고, 단일 요약 연산자를 포함 하 고, 하나 이상의 그룹으로 집계 된 하나 이상의 집계 함수를 식에 포함 해야 합니다. 요약 연산자는 항상 쿼리의 마지막 연산자 여야 합니다.

* 뷰는 `arg_max` / `arg_min` / `any` 뷰 (동일한 뷰에서 함께 사용할 수 있는 함수) 또는 다른 지원 되는 함수 중 하나 이지만 구체화 된 뷰는 동일 하지 않습니다. 
    예를 들어은 `SourceTable | summarize arg_max(Timestamp, *), count() by Id` 지원 되지 않습니다. 

* 쿼리에는 또는에 종속 된 연산자가 포함 되지 않아야 합니다 `now()` `ingestion_time()` . 예를 들어 쿼리에는가 없어야 `where Timestamp > ago(5d)` 합니다. 집계를 사용 하는 구체화 된 뷰는 `arg_max` / `arg_min` / `any` 지원 되는 다른 집계 함수를 포함할 수 없습니다. 구체화 된 뷰에서 보존 정책을 사용 하 여 뷰가 적용 되는 시간을 제한 합니다.

* 복합 집계는 구체화 된 뷰 정의에서 지원 되지 않습니다. 예를 들어 다음과 같은 뷰 대신 `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` 구체화 된 뷰를로 정의 `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` 합니다. 쿼리 시간 보기 중에를 실행 `ViewName | project Id, Result=a/b` 합니다. 계산 열 ()을 포함 하 여 뷰의 필요한 출력을 `a/b` [저장 된 함수](../../query/functions/user-defined-functions.md)에 캡슐화 할 수 있습니다. 구체화 된 뷰에 직접 액세스 하지 않고 저장 된 함수에 액세스 합니다.

* 클러스터 간/데이터베이스 간 쿼리는 지원 되지 않습니다.

* [External_table ()](../../query/externaltablefunction.md) 및 [externaldata](../../query/externaldata-operator.md) 에 대 한 참조는 지원 되지 않습니다.

* 뷰의 원본 테이블 외에도 하나 이상의를 참조할 수 있습니다 [`dimension tables`](../../concepts/fact-and-dimension-tables.md) . 차원 테이블은 뷰 속성에서 명시적으로 호출 해야 합니다. 차원 테이블과 조인 하는 경우의 동작을 이해 하는 것이 중요 합니다.

    * 뷰의 원본 테이블 (팩트 테이블)의 레코드는 한 번만 구체화 됩니다. 팩트 테이블과 차원 테이블 사이에 다른 수집 대기 시간이 있으면 뷰 결과에 영향을 줄 수 있습니다.

    * **예**: 뷰 정의에는 차원 테이블과의 내부 조인이 포함 됩니다. 구체화 시 차원 레코드는 완전히 수집 않지만 이미 팩트 테이블에 수집 되었습니다. 이 레코드는 뷰에서 삭제 되 고 다시 처리 되지 않습니다. 

        마찬가지로 조인이 외부 조인 인 경우 팩트 테이블의 레코드가 처리 되 고 차원 테이블 열에 대해 null 값이 포함 된 보기에 추가 됩니다. 뷰에 이미 추가 된 (null 값 포함) 레코드는 다시 처리 되지 않습니다. 차원 테이블의 열에서 해당 값은 null로 유지 됩니다.

## <a name="properties"></a>속성

다음은 절에서 지원 됩니다 `with(propertyName=propertyValue)` . 모든 속성은 선택 사항입니다.

|속성|Type|Description |
|----------------|-------|---|
|가득|bool|현재 *SourceTable* ()에 있는 모든 레코드를 기반으로 뷰를 만들지 아니면 `true` "온-" ()로 만들지 여부를 지정 `false` 합니다. 기본값은 `false`입니다.| 
|effectiveDateTime|Datetime| 와 함께 지정 하 `backfill=true` 는 경우 datetime 이후의 레코드를 수집 하는 backfills 생성 됩니다. 또한 백필을 true로 설정 해야 합니다. 에는 datetime 리터럴이 필요 합니다 (예:). `effectiveDateTime=datetime(2019-05-01)`|
|dimensionTables|배열|뷰의 차원 테이블을 쉼표로 구분한 목록입니다. [쿼리 인수](#query-argument) 참조
|autoUpdateSchema|bool|원본 테이블 변경 내용에 대 한 뷰를 자동으로 업데이트할지 여부를 지정 합니다. 기본값은 `false`입니다. 이 옵션은 유형 보기에 대해서만 유효 `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` 합니다 (열 인수가 인 경우에만 `*` ). 이 옵션을 true로 설정 하면 원본 테이블에 대 한 변경 내용이 구체화 된 뷰에 자동으로 반영 됩니다.
|폴더|문자열|구체화 된 뷰의 폴더입니다.|
|docString|문자열|구체화 된 뷰를 문서화 하는 문자열|

> [!WARNING]
> * `autoUpdateSchema`원본 테이블의 열을 삭제 하면를 사용 하면 데이터가 영구적으로 손실 될 수 있습니다. 
> * 원본 테이블을 변경 하 여 구체화 된 뷰로 스키마를 변경 하 고 `autoUpdateSchema` 이 false 이면 뷰가 자동으로 비활성화 됩니다. 
>    * 이 오류는를 사용 하 `arg_max(Timestamp, *)` 고 원본 테이블에 열을 추가할 때 일반적으로 발생 합니다. 
>    * 뷰 쿼리를로 정의 하거나 옵션을 사용 하 여이 오류를 방지 `arg_max(Timestamp, Column1, Column2, ...)` `autoUpdateSchema` 합니다.
> * 이러한 이유로 보기를 사용 하지 않도록 설정 하면 [구체화 된 뷰 사용](materialized-view-enable-disable.md) 명령을 사용 하 여 문제를 해결 한 후 다시 사용 하도록 설정할 수 있습니다.
>

## <a name="examples"></a>예제

1. 현재 수집 레코드를 구체화 하는 빈 arg_max 보기를 만듭니다.

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. 다음을 사용 하 여 백필 옵션을 사용 하 여 일일 집계에 대 한 구체화 된 뷰를 만듭니다 `async` .

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, docString="Customer telemetry") CustomerUsage on table T
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day 
    } 
    ```
    
1. 백필 및를 사용 하 여 구체화 된 뷰를 만듭니다 `effectiveDateTime` . 뷰는 datetime의 레코드를 기반으로 생성 됩니다.

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, effectiveDateTime=datetime(2019-01-01)) CustomerUsage on table T 
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day
    } 
    ```
1. EventId 열을 기반으로 하 여 원본 테이블의 복제를 취소 하는 구체화 된 뷰입니다.

    <!-- csl -->
    ```
    .create materialized-view DedupedT on table T
    {
        T
        | summarize any(*) by EventId
    }
    ```

1. 이 정의에는 문 앞에 추가 연산자가 포함 될 수 있습니다 `summarize` `summarize` .

    <!-- csl -->
    ```
    .create materialized-view CustomerUsage on table T 
    {
        T 
        | where Customer in ("Customer1", "Customer2", "CustomerN")
        | parse Url with "https://contoso.com/" Api "/" *
        | extend Month = startofmonth(Timestamp)
        | summarize count(), dcount(User), max(Duration) by Customer, Api, Month
    }
    ```

1. 차원 테이블과 조인 하는 구체화 된 뷰:

    <!-- csl -->
    ```
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        DimUsers | project User, Age, Address
        | join kind=rightouter hint.strategy=broadcast T on User
        | summarize arg_max(Timestamp, *) by User 
    }
    ```
    

## <a name="supported-aggregation-functions"></a>지원 되는 집계 함수

지원 되는 집계 함수는 다음과 같습니다.

* [`count`](../../query/count-aggfunction.md)
* [`countif`](../../query/countif-aggfunction.md)
* [`dcount`](../../query/dcount-aggfunction.md)
* [`dcountif`](../../query/dcountif-aggfunction.md)
* [`min`](../../query/min-aggfunction.md)
* [`max`](../../query/max-aggfunction.md)
* [`avg`](../../query/avg-aggfunction.md)
* [`avgif`](../../query/avgif-aggfunction.md)
* [`sum`](../../query/sum-aggfunction.md)
* [`arg_max`](../../query/arg-max-aggfunction.md)
* [`arg_min`](../../query/arg-min-aggfunction.md)
* [`any`](../../query/any-aggfunction.md)
* [`hll`](../../query/hll-aggfunction.md)
* [`make_set`](../../query/makeset-aggfunction.md)
* [`make_list`](../../query/makelist-aggfunction.md)
* [`percentile`, `percentiles`](../../query/percentiles-aggfunction.md)

## <a name="performance-tips"></a>성능 팁

* 구체화 된 뷰 쿼리 필터는 구체화 된 뷰 차원 (집계 기준-절) 중 하나를 기준으로 필터링 할 때 최적화 됩니다. 쿼리 패턴은 구체화 된 뷰의 차원이 될 수 있는 일부 열을 기준으로 필터링 하는 경우가 많습니다. 예를 들어에 의해 필터링 되는을 통해를 노출 하는 구체화 된 뷰의 경우 `arg_max` `ResourceId` `SubscriptionId` 권장 사항은 다음과 같습니다.

    **Do**:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    **수행 하지 않음**:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* 구체화 된 뷰 정의의 일부로 [업데이트 정책](../updatepolicy.md) 으로 이동할 수 있는 변환, normalizations 및 기타 많은 계산을 포함 하지 않습니다. 대신이 모든 프로세스를 업데이트 정책에서 수행 하 고 구체화 된 뷰에서만 집계를 수행 합니다. 해당 하는 경우 차원 테이블에서 조회에이 프로세스를 사용 합니다.

    **Do**:
    
    * 업데이트 정책:
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
        "IsTransactional": false}]'  
    ```
        
    * 구체화 된 뷰:
    
    ```kusto
    .create materialized-view Usage on table Events
    {
    &nbsp;     Target 
    &nbsp;     | summarize count() by ResourceId 
    }
    ```
    
    **수행 하지 않음**:
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
    &nbsp;     SourceTable 
    &nbsp;     | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
    &nbsp;     | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> 최상의 쿼리 시간 성능이 필요 하지만 일부 데이터의 유효성을 저하 시킬 수 있는 경우 [materialized_view () 함수](../../query/materialized-view-function.md)를 사용 합니다.

## <a name="limitations-on-creating-materialized-views"></a>구체화 된 뷰를 만들 때의 제한 사항

* 구체화 된 뷰를 만들 수 없습니다.
    * 구체화 된 다른 뷰를 기반으로 합니다.
    * 팔로 워 [데이터베이스](../../../follower.md). 종동체 데이터베이스는 읽기 전용 이며 구체화 된 뷰는 쓰기 작업이 필요 합니다.  리더 데이터베이스에 정의 된 구체화 된 뷰는 리더의 다른 테이블과 마찬가지로 팔로 워에서 쿼리할 수 있습니다. 
* 구체화 된 뷰의 원본 테이블:
    * 는 수집 [방법](../../../ingest-data-overview.md#ingestion-methods-and-tools)중 하나를 사용 하거나 [업데이트 정책을](../updatepolicy.md)사용 하거나 [쿼리 명령에서 수집](../data-ingestion/ingest-from-query.md)하 여 직접 수집 되는 테이블 이어야 합니다.
        * 특히 다른 테이블의 [익스텐트](../move-extents.md) 를 구체화 된 뷰의 원본 테이블로 사용 하는 것은 지원 되지 않습니다. 다음 오류가 발생 하 여 이동 익스텐트가 실패할 수 있습니다. `Cannot drop/move extents from/to table 'TableName' since Materialized View 'ViewName' is currently processing some of these extents` . 
    * [IngestionTime 정책이](../ingestiontimepolicy.md) 활성화 되어 있어야 합니다 (기본값 사용).
    * 스트리밍 수집을 사용 하도록 설정할 수 없습니다.
    * 제한 된 테이블이 나 행 수준 보안이 설정 된 테이블 일 수 없습니다.
* 구체화 된 뷰 위에는 [커서 함수](../databasecursor.md#cursor-functions) 를 사용할 수 없습니다.
* 구체화 된 뷰에서 연속 내보내기가 지원 되지 않습니다.

## <a name="cancel-materialized-view-creation"></a>구체화 된 뷰 만들기 취소

옵션을 사용할 때 구체화 된 뷰 만들기 프로세스를 취소 `backfill` 합니다. 이 작업은 만들기가 너무 오래 걸리고 실행 중에 중단 하려는 경우에 유용 합니다.  

> [!WARNING]
> 이 명령을 실행 한 후에는 구체화 된 뷰를 복원할 수 없습니다.

만들기 프로세스를 즉시 중단할 수 없습니다. Cancel 명령은 구체화를 중지 하도록 신호를 보내고 취소가 요청 되었는지 주기적으로 확인 합니다. 취소 명령은 구체화 된 뷰 만들기 프로세스가 취소 될 때까지 최대 10 분 동안 기다린 후 취소가 성공 하면 다시 보고 합니다. 취소가 10 분 이내에 성공 하지 않은 경우에도 취소 명령이 실패를 보고 하는 경우에도 구체화 된 뷰는 나중에 생성 프로세스에서 자체적으로 중단 될 수 있습니다. [. 작업 표시](../operations.md#show-operations) 명령은 작업이 취소 되었는지 여부를 나타냅니다. `cancel operation`명령은 구체화 된 뷰 생성 취소에 대해서만 지원 되며 다른 작업을 취소 하는 데에는 지원 되지 않습니다.

### <a name="syntax"></a>구문

`.cancel``operation` *operationId*

### <a name="properties"></a>속성

|속성|Type|Description
|----------------|-------|---|
|operationId|GUID|구체화 된 뷰 만들기 명령에서 반환 된 작업 ID입니다.|

### <a name="output"></a>출력

|출력 매개 변수 |Type |Description
|---|---|---
|OperationId|GUID|구체화 된 뷰 만들기 명령의 작업 ID입니다.
|작업(Operation)|String|작업 종류입니다.
|StartedOn|Datetime|만들기 작업의 시작 시간입니다.
|CancellationState|문자열|- `Cancelled successfully` (생성이 취소 된 경우), ( `Cancellation failed` 취소 시간이 초과 될 때까지 대기), ( `Unknown` 뷰 생성이 더 이상 실행 되지 않고이 작업으로 인해 취소 되지 않음)
|ReasonPhrase|문자열|취소가 실패 한 이유입니다.

### <a name="example"></a>예제

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|작업(Operation)|StartedOn|CancellationState|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|MaterializedViewCreateOrAlter|2020-05-08 19:45:03.9184142|취소 됨||

취소가 10 분 이내에 완료 되지 않은 경우 `CancellationState` 는 실패를 표시 합니다. 그런 다음 만들기를 중단할 수 있습니다.
