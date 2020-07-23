---
title: 데이터 제거-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 제거에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 05/12/2020
ms.openlocfilehash: ad659f9208bd057719a1adc31f8370c0cb11ffd3
ms.sourcegitcommit: fb54d71660391a63b0c107a9703adea09bfc7cb9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946141"
---
# <a name="data-purge"></a>데이터 제거

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

데이터 플랫폼인 Azure 데이터 탐색기는 Kusto 및 관련 된 명령을 사용 하 여 개별 레코드를 삭제 하는 기능을 지원 합니다 `.purge` . [전체 테이블을 제거할](#purging-an-entire-table)수도 있습니다.  

> [!WARNING]
> 명령을 통해 데이터를 삭제 하는 `.purge` 것은 개인 데이터를 보호 하는 데 사용 하도록 설계 되었으므로 다른 시나리오에서 사용 하면 안 됩니다. 자주 삭제 요청을 지원 하거나 대량의 데이터를 삭제할 수 없으며 서비스에 상당한 성능 영향을 줄 수 있습니다.

## <a name="purge-guidelines"></a>제거 지침

Azure 데이터 탐색기에 개인 데이터를 저장 하기 전에 데이터 스키마를 신중 하 게 디자인 하 고 관련 정책을 조사 하세요.

1. 최상의 시나리오에서이 데이터에 대 한 보존 기간은 충분히 짧고 데이터가 자동으로 삭제 됩니다.
1. 보존 기간 사용이 불가능 한 경우 적은 수의 Azure 데이터 탐색기 테이블에서 개인 정보 취급 방침에 적용 되는 모든 데이터를 격리 합니다. 최적으로, 하나의 테이블만 사용 하 고 다른 모든 테이블에서 해당 테이블에 연결 합니다. 이러한 격리를 사용 하면 중요 한 데이터를 보유 하 고 있는 적은 수의 테이블에서 데이터 [제거 프로세스](#purge-process) 를 실행 하 고 다른 모든 테이블을 피할 수 있습니다.
1. 호출자는 `.purge` 매일 테이블당 1-2 명령에 대 한 명령 실행을 일괄 처리 하려고 합니다. 고유한 사용자 id 조건자를 사용 하 여 여러 명령을 실행 하지 마세요. 대신 제거 해야 하는 모든 사용자 id를 포함 하는 단일 명령을 보냅니다.

## <a name="purge-process"></a>프로세스 제거

Azure 데이터 탐색기에서 데이터를 선택적으로 제거 하는 프로세스는 다음 단계에서 수행 됩니다.

1. 1 단계: Azure 데이터 탐색기 테이블 이름 및 레코드 별 조건자를 사용 하 여 입력을 제공 하 고 삭제할 레코드를 나타냅니다. Kusto는 테이블을 검색 하 여 데이터 제거에 참여 하는 데이터 범위를 식별 합니다. 지정 된 익스텐트는 조건자가 true를 반환 하는 하나 이상의 레코드를 포함 하는 범위입니다.
1. 2 단계: (일시 삭제) 테이블의 각 데이터 범위 (1)를 reingested 버전으로 바꿉니다. Reingested 버전에는 조건자가 true를 반환 하는 레코드가 없어야 합니다. 새 데이터가 테이블에 수집 되는 경우이 단계가 끝나면 쿼리에서 조건자가 true를 반환 하는 데이터가 더 이상 반환 되지 않습니다. 제거 일시 삭제 단계의 기간은 다음 매개 변수에 따라 달라 집니다. 
     * 제거 해야 하는 레코드 수 
     * 클러스터의 데이터 익스텐트 간 배포 기록 
     * 클러스터의 노드 수  
     * 제거 작업에 대해 보유 한 예비 용량
     * 2 단계 기간이 몇 초에서 몇 시간 사이에 다를 수 있는 몇 가지 다른 요인이 있습니다.
1. 3 단계: (하드 삭제) "포이즌" 데이터를 포함할 수 있는 모든 저장소 아티팩트를 다시 사용 하 고 저장소에서 삭제 합니다. 이 단계는 이전 단계를 완료 한 후 최소 5 일이 지난 후 30 일이 지난 후에 수행 됩니다. 이러한 타임 라인은 데이터 개인 정보 요구 사항에 따라 설정 됩니다.

명령을 실행 하면 `.purge` 이 프로세스가 트리거되고 완료 하는 데 몇 일이 걸립니다. 조건자가 적용 되는 레코드의 밀도가 충분히 크면 프로세스에서 테이블의 모든 데이터를 효과적으로 다시 수집 합니다. 이러한 다시 수집은 성능 및 COGS에 상당한 영향을 미칩니다.

## <a name="purge-limitations-and-considerations"></a>제한 사항 및 고려 사항 제거

* 제거 프로세스는 마지막 이며 취소할 수 없습니다. 이 프로세스를 실행 취소 하거나 제거 된 데이터를 복구할 수 없습니다. [실행 취소 테이블 삭제](../management/undo-drop-table-command.md) 와 같은 명령은 제거 된 데이터를 복구할 수 없습니다. 이전 버전으로 데이터를 롤백하는 것은 최신 제거 명령 앞으로 이동할 수 없습니다.

* 제거를 실행 하기 전에 쿼리를 실행 하 고 결과가 예상 결과와 일치 하는지 확인 하 여 조건자를 확인 합니다. 또한 제거 될 예상 레코드 수를 반환 하는 2 단계 프로세스를 사용할 수 있습니다. 

* `.purge`명령은 데이터 관리 끝점에 대해 실행 됩니다 `https://ingest-[YourClusterName].[region].kusto.windows.net` .
   이 명령에는 관련 데이터베이스에 대 한 [데이터베이스 관리자](../management/access-control/role-based-authorization.md) 권한이 필요 합니다. 
* 제거 프로세스 성능에 영향을 주거나 [제거 지침](#purge-guidelines) 을 준수 하도록 하기 위해 호출자는 최소한의 테이블에 관련 데이터가 포함 되도록 데이터 스키마를 수정 하 고 테이블 마다 일괄 처리 명령을 수행 하 여 제거 프로세스의 심각한 COGS 영향을 줄여야 합니다.
* Purge `predicate` 명령의 매개 변수 [.purge](#purge-table-tablename-records-command) 는 제거할 레코드를 지정 하는 데 사용 됩니다.
`Predicate`크기는 63 KB로 제한 됩니다. 을 생성할 때 `predicate` :
    * [' In ' 연산자](../query/inoperator.md)를 사용 합니다 (예:) `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` . 
    * [' In ' 연산자](../query/inoperator.md) 의 제한을 확인 합니다 (목록에 최대 값이 포함 될 수 있음 `1,000,000` ).
    * 쿼리 크기가 큰 경우 [ `externaldata` 연산자](../query/externaldata-operator.md)를 사용 합니다 (예:) `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` . 파일은 제거할 Id의 목록을 저장 합니다.
    * 모든 `externaldata` blob (전체 blob의 전체 크기)을 확장 한 후 총 쿼리 크기는 64 MB를 초과할 수 없습니다. 

## <a name="purge-performance"></a>성능 제거

지정 된 시간에 하나의 제거 요청만 클러스터에서 실행할 수 있습니다. 다른 모든 요청은 상태에서 큐에 대기 됩니다 `Scheduled` . 제거 요청 큐 크기를 모니터링 하 고, 데이터에 적용 되는 요구 사항에 맞게 적절 한 제한 내에 유지 합니다.

제거 실행 시간을 줄이려면 다음을 수행 합니다.
* 제거 [지침](#purge-guidelines) 에 따라 제거 된 데이터의 양을 줄입니다.
* 콜드 데이터를 제거 하는 데 더 오래 걸리고 있으므로 [캐싱 정책을](../management/cachepolicy.md) 조정 합니다.
* 클러스터 확장

* [익스텐트 제거 다시 작성 용량](../management/capacitypolicy.md#extents-purge-rebuild-capacity)에 자세히 설명 된 대로 신중 하 게 고려 하 여 클러스터 제거 용량을 늘립니다. 이 매개 변수를 변경 하려면 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 을 열어야 합니다.

## <a name="trigger-the-purge-process"></a>제거 프로세스 트리거

> [!Note]
> 제거 실행은 데이터 관리 끝점 [해당 clustername]에서 [purge Table *TableName* records](#purge-table-tablename-records-command) 명령을 실행 하 여 호출 됩니다 https://ingest- . Region] .kusto. net.

### <a name="purge-table-tablename-records-command"></a>테이블 TableName 레코드 제거 명령

다른 사용 시나리오에 대 한 두 가지 방법으로 제거 명령이 호출 될 수 있습니다.

* 프로그래밍 방식 호출: 응용 프로그램에서 호출 하는 단일 단계입니다. 이 명령을 호출 하면 제거 실행 시퀀스가 바로 트리거됩니다.

    **구문**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > [Kusto 클라이언트 라이브러리](../api/netfx/about-kusto-data.md) NuGet 패키지의 일부로 사용할 수 있는 CslCommandGenerator API를 사용 하 여이 명령을 생성 합니다.

* 인간 호출: 별도의 단계로 명시적 확인이 필요한 2 단계 프로세스입니다. 명령의 첫 번째 호출은 실제 제거를 실행 하기 위해 제공 해야 하는 확인 토큰을 반환 합니다. 이 순서는 잘못 된 데이터를 실수로 삭제 하는 위험을 줄여 줍니다. 이 옵션을 사용 하면 상당한 콜드 캐시 데이터가 있는 큰 테이블에서 완료 하는 데 시간이 오래 걸릴 수 있습니다.
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **구문**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    | 매개 변수  | Description  |
    |---------|---------|
    | `DatabaseName`   |   데이터베이스 이름      |
    | `TableName`     |     테이블 이름입니다.    |
    | `Predicate`    |    제거할 레코드를 식별 합니다. 아래의 제거 조건자 제한 사항을 참조 하세요. | 
    | `noregrets`    |     설정 되 면 단일 단계 활성화를 트리거합니다.    |
    | `verificationtoken`     |  2 단계 활성화 시나리오에서 ( `noregrets` 설정 되지 않음)이 토큰을 사용 하 여 두 번째 단계를 실행 하 고 작업을 커밋할 수 있습니다. 가 `verificationtoken` 지정 되지 않으면 명령의 첫 번째 단계를 트리거합니다. 제거에 대 한 정보는 #2 단계를 수행 하기 위해 명령에 다시 전달 해야 하는 토큰과 함께 반환 됩니다.   |

    **제거 조건자 제한 사항**

    * 조건자는 *where [ColumnName] == 'X'*  /  *(' x ', ' Y ', ' Z ') 및 [othercolumn] = = ' a '와*같이 간단한 선택 항목 이어야 합니다 (예: where [columnname] = = ' X ' where [columnname]).
    * 여러 필터를 별도 절이 아닌 ' and '와 결합 해야 `where` 합니다 (예: `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` 및 not `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` ).
    * 조건자는 제거 중인 테이블 (*TableName*) 이외의 다른 테이블을 참조할 수 없습니다. 조건자에는 선택 문 ()만 포함 될 수 있습니다 `where` . 테이블에서 특정 열을 프로젝션 할 수 없습니다 (출력 스키마를 실행 하는* 경우). `table` ' Predicate*'는 테이블 스키마와 일치 해야 합니다.
    * 시스템 함수 (예:, `ingestion_time()` , `extent_id()` )는 지원 되지 않습니다.

#### <a name="example-two-step-purge"></a>예: 2 단계 제거

2 단계 활성화 시나리오에서 제거를 시작 하려면 명령의 #1 단계를 실행 합니다.

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | NumRecordsToPurge | EstimatedPurgeExecutionTime| VerificationToken
    |--|--|--
    | 1596 | 00:00:02 | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Then, validate the NumRecordsToPurge before running step #2. 

2 단계 활성화 시나리오에서 제거를 완료 하려면 #1 단계에서 반환 된 확인 토큰을 사용 하 여 #2 단계를 실행 합니다.

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Output**

    | `OperationId` | `DatabaseName` | `TableName`|`ScheduledTime` | `Duration` | `LastUpdatedOn` |`EngineOperationId` | `State` | `StateDetails` |`EngineStartTime` | `EngineDuration` | `Retries` |`ClientRequestId` | `Principal`|
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--|
    | c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |예약됨 | | | |0 |KE. RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 id = ...|

#### <a name="example-single-step-purge"></a>예: 단일 단계 제거

단일 단계 활성화 시나리오에서 제거를 트리거하려면 다음 명령을 실행 합니다.

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
    ```

**출력**

| `OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |예약됨 | | | |0 |KE. RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 id = ...|

### <a name="cancel-purge-operation-command"></a>제거 작업 취소 명령

필요한 경우 보류 중인 제거 요청을 취소할 수 있습니다.

> [!NOTE]
> 이 작업은 오류 복구 시나리오를 위한 것입니다. 정상적으로 작동 하는 것은 아니지만 정상적으로 작동 하는 것은 아닙니다. 큐에 대기 중인 요청에만 적용 될 수 있습니다 (실행을 위해 엔진 노드로 아직 디스패치 되지 않음). 데이터 관리 끝점에서 명령이 실행 됩니다.

**구문**

```kusto
 .cancel purge <OperationId>
 ```

**예제**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**출력**

이 명령의 출력은 취소 중인 제거 작업의 업데이트 된 상태를 보여 주는 ' 제거 *OperationId OperationId*' 명령 출력과 동일 합니다. 시도에 성공 하면 작업 상태가로 업데이트 됩니다 `Abandoned` . 그렇지 않으면 작업 상태가 변경 되지 않습니다. 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |KE. RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 id = ...

## <a name="track-purge-operation-status"></a>제거 작업 상태 추적 

> [!Note]
> 제거 작업은 데이터 관리 끝점 [해당 clustername]에 대해 실행 되는 [제거 표시](#show-purges-command) 명령을 사용 하 여 추적할 수 있습니다 https://ingest- . region] .kusto. net.

상태 = ' 완료 '는 제거 작업의 첫 번째 단계가 성공적으로 완료 되었음을 나타냅니다. 즉, 레코드가 일시 삭제 되 고 더 이상 쿼리에 사용할 수 없습니다. 고객은 두 번째 단계 (하드 삭제) 완료를 추적 하 고 **확인할 필요가 없습니다** . 이 단계는 Azure 데이터 탐색기에서 내부적으로 모니터링 됩니다.

### <a name="show-purges-command"></a>제거 명령 표시

`Show purges`명령에서 요청 된 기간 내에 작업 ID를 지정 하 여 작업 상태 제거를 표시 합니다. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

| 속성  |Description  |필수/선택 사항|
|---------|------------|-------|
|`OperationId `   |      단일 단계 또는 두 번째 단계를 실행 한 후 데이터 관리 작업 Id가 출력 됩니다.   |필수
|`StartDate`    |   필터링 작업의 시간 제한이 더 낮습니다. 생략 하는 경우 기본값은 현재 시간 보다 24 시간입니다.      |선택 사항
|`EndDate`    |  필터링 작업의 상한 시간 제한입니다. 생략 하는 경우 기본값은 현재 시간입니다.       |선택 사항
|`DatabaseName`    |     결과를 필터링 할 데이터베이스 이름입니다.    |선택 사항

> [!NOTE]
> 상태는 클라이언트에 [데이터베이스 관리자](../management/access-control/role-based-authorization.md) 권한이 있는 데이터베이스에만 제공 됩니다.

**예**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**출력** 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |완료됨 |제거가 완료 되었습니다 (저장소 아티팩트 삭제 보류 중). |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |KE. RunCommand; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 id = ...

* `OperationId`-제거를 실행할 때 반환 되는 DM 작업 ID입니다. 
* `DatabaseName`* *-데이터베이스 이름 (대/소문자 구분). 
* `TableName`-테이블 이름 (대/소문자 구분)입니다. 
* `ScheduledTime`-DM 서비스에 대 한 제거 명령을 실행 하는 시간입니다. 
* `Duration`-실행 DM 큐 대기 시간을 포함 하 여 제거 작업의 총 기간입니다. 
* `EngineOperationId`-엔진에서 실행 중인 실제 제거의 작업 ID입니다. 
* `State`-제거 상태 이며 다음 값 중 하나일 수 있습니다. 
    * `Scheduled`-제거 작업이 실행 되도록 예약 되었습니다. 작업이 예약 된 상태로 유지 되는 경우 제거 작업의 백로그가 있을 수 있습니다. 이 백로그를 지우려면 [성능 제거](#purge-performance) 를 참조 하세요. 일시적 오류 발생 시 제거 작업이 실패 하는 경우 DM에서 다시 시도 되 고 다시 예약 됨으로 설정 됩니다. 따라서 예약 됨에서 InProgress로, 다시 예약 됨에서 작업으로 전환 되는 것을 볼 수 있습니다.
    * `InProgress`-제거 작업이 엔진에서 진행 중입니다. 
    * `Completed`-제거를 완료 했습니다.
    * `BadInput`-잘못 된 입력을 제거 하지 않았으므로 다시 시도 되지 않습니다. 이 오류는 조건자의 구문 오류, 제거 명령에 대 한 잘못 된 조건자, 제한을 초과 하는 쿼리 (예: 연산자의 1M 엔터티 `externaldata` 이상 또는 64의 총 확장 된 쿼리 크기) 403 404와 같은 다양 한 문제 때문에 발생할 수 있습니다 `externaldata` .
    * `Failed`-제거 하지 못했으며 다시 시도 되지 않습니다. 이 오류는 다른 제거 작업의 백로그가 나 다시 시도 제한을 초과 하는 여러 오류가 발생 하 여 작업이 큐에서 너무 오래 (14 일 이상) 대기 중인 경우에 발생할 수 있습니다. 후자는 내부 모니터링 경고를 발생 시키고 Azure 데이터 탐색기 팀에서 조사 됩니다. 
* `StateDetails`-상태에 대 한 설명입니다.
* `EngineStartTime`-엔진에 명령이 실행 된 시간입니다. 이 시간과 ScheduledTime 사이에 큰 차이가 있는 경우 일반적으로 제거 작업의 중요 한 백로그가 있으며 클러스터는 속도를 유지 하지 않습니다. 
* `EngineDuration`-엔진에서의 실제 삭제 실행 시간입니다. 제거를 여러 번 다시 시도 하는 경우 모든 실행 기간의 합계입니다. 
* `Retries`-일시적인 오류로 인해 DM 서비스에서 작업을 다시 시도한 횟수입니다.
* `ClientRequestId`-DM 제거 요청의 클라이언트 활동 ID입니다. 
* `Principal`-제거 명령 발급자의 id입니다.

## <a name="purging-an-entire-table"></a>전체 테이블 제거

테이블을 삭제 하면 테이블을 삭제 하 고 삭제 된 것으로 표시 하 여 [제거 프로세스](#purge-process) 에 설명 된 하드 삭제 프로세스가 실행 됩니다. 제거 하지 않고 테이블을 삭제 하면 모든 저장소 아티팩트가 삭제 되지 않습니다. 이러한 아티팩트는 테이블에 처음 설정 된 하드 보존 정책에 따라 삭제 됩니다. `purge table allrecords`명령은 빠르고 효율적 이며, 시나리오에 해당 하는 경우 레코드 제거 프로세스 보다 더 좋습니다. 

> [!Note]
> 명령은 데이터 관리 끝점 [해당 clustername]에서 [제거 테이블 *TableName* allrecords](#purge-table-tablename-allrecords-command) 명령을 실행 하 여 호출 됩니다 https://ingest- . region] .kusto. net.

### <a name="purge-table-tablename-allrecords-command"></a>테이블 제거 *TableName* allrecords 명령

'[. 제거 테이블 레코드 ](#purge-table-tablename-records-command)' 명령과 마찬가지로이 명령은 프로그래밍 방식 (단일 단계) 또는 수동 (2 단계) 모드에서 호출할 수 있습니다.

1. 프로그래밍 방식 호출 (1 단계):

     **구문**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

1. 인간 호출 (2 단계):

     **구문**

     ```kusto
   
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)

     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    | 매개 변수  |Description  |
    |---------|---------|
    | `DatabaseName`   |   데이터베이스의 이름입니다.      |
    | `TableName`    |     테이블 이름입니다.    |
    | `noregrets`    |     설정 되 면 단일 단계 활성화를 트리거합니다.    |
    | `verificationtoken`     |  2 단계 활성화 시나리오 ( `noregrets` 설정 되지 않음)에서는이 토큰을 사용 하 여 두 번째 단계를 실행 하 고 작업을 커밋할 수 있습니다. 가 `verificationtoken` 지정 되지 않으면 명령의 첫 번째 단계를 트리거합니다. 이 단계에서는 토큰을 반환 하 여 명령에 다시 전달 하 고 #2 단계를 수행 합니다.|

#### <a name="example-two-step-purge"></a>예: 2 단계 제거

1. 2 단계 활성화 시나리오에서 제거를 시작 하려면 명령의 #1 단계를 실행 합니다. 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **출력**

    | `VerificationToken`|
    |--|
    | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b|

1.  2 단계 활성화 시나리오에서 제거를 완료 하려면 #1 단계에서 반환 된 확인 토큰을 사용 하 여 #2 단계를 실행 합니다.

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    출력은 '. show tables ' 명령 출력과 동일 합니다 (제거 된 테이블 없이 반환 됨).

    **출력**

    |  TableName|DatabaseName|폴더|DocString
    |---|---|---|---
    |  OtherTable|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>예: 단일 단계 제거

단일 단계 활성화 시나리오에서 제거를 트리거하려면 다음 명령을 실행 합니다.

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

출력은 '. show tables ' 명령 출력과 동일 합니다 (제거 된 테이블 없이 반환 됨).

**출력**

|TableName|DatabaseName|폴더|DocString
|---|---|---|---
|OtherTable|MyDatabase|---|---

