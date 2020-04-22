---
title: 데이터 제거 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 제거에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 460ad9cfca4f97e6735d30a4d47d6384581e7af7
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82029993"
---
# <a name="data-purge"></a>데이터 제거

>[!Note]
> 이 문서에서는 디바이스 또는 서비스에서 개인 데이터를 삭제하는 방법에 대한 단계를 제공하며 GDPR에 따라 의무를 지원하는 데 사용할 수 있습니다. GDPR에 대한 일반 정보를 찾고 있는 경우 [서비스 신뢰 포털의 GDPR 섹션을](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)참조하십시오.



데이터 플랫폼인 Kusto(Azure Data Explorer)는 관련 명령을 사용하여 `.purge` 개별 레코드를 삭제하는 기능을 지원합니다. [전체 테이블을 제거할](#purging-an-entire-table)수도 있습니다.  

> [!WARNING]
> `.purge` 명령을 통한 데이터 삭제는 개인 데이터를 보호하는 데 사용되며 다른 시나리오에서는 사용해서는 안 됩니다. 빈번한 삭제 요청이나 대량의 데이터 삭제를 지원하도록 설계되지 않았으며 서비스에 상당한 성능 영향을 미칠 수 있습니다.

## <a name="purge-guidelines"></a>제거 지침

Azure Data Explorer에 개인 데이터를 저장하는 팀은 데이터 스키마를 신중하게 디자인하고 관련 정책을 조사한 후에만 개인 데이터를 저장하는 **것이 좋습니다.**

1. 최상의 시나리오에서는 이 데이터의 보존 기간이 충분히 짧고 데이터가 자동으로 삭제됩니다.
2. 보존 기간 사용을 사용할 수 없는 경우 소수의 Kusto 테이블(최적으로 하나의 테이블)에서 개인 정보 보호 규칙이 적용되는 모든 데이터를 격리하고 다른 모든 테이블에서 연결하는 것이 좋습니다. 이렇게 하면 중요한 데이터를 보유한 소수의 테이블에서 데이터 [제거 프로세스를](#purge-process) 실행하고 다른 모든 테이블을 피할 수 있습니다.
3. 호출자는 명령 실행을 `.purge` 하루에 테이블당 1-2개의 명령으로 일괄 처리하려고 모든 시도를 해야 합니다.
   각각 고유한 사용자 ID 조건자를 가진 여러 명령을 발행하지 마십시오. 대신, 그 술어는 제거를 필요로 하는 모든 사용자 ID를 포함하는 단일 명령을 보냅니다.

## <a name="purge-process"></a>제거 프로세스

Azure Data Explorer에서 데이터를 선택적으로 제거하는 프로세스는 다음 단계에서 수행됩니다.

1. **1단계:** Kusto 테이블 이름과 삭제할 레코드를 나타내는 레코드별 술어가 주어지면 Kusto는 데이터 제거에 참여할 데이터 샤드를 식별하기 위해 테이블을 검사합니다(술어가 true를 반환하는 하나 이상의 레코드가 있음).
2. **2단계: (소프트 삭제)** 테이블의 각 데이터 샤드(단계(1)에서 식별)를 조건어가 true로 반환하는 레코드가 없는 다시 수집된 버전으로 바꿉니다.
   새 데이터가 테이블에 수집되지 않는 한 이 단계가 끝날 때까지 조건어가 true를 반환하는 데이터를 더 이상 반환하지 않습니다. 
   제거 소프트 삭제 단계의 기간은 제거해야 하는 레코드 수, 클러스터의 데이터 샤드 에 대한 배포, 클러스터의 노드 수, 제거 작업에 대한 예비 용량 및 기타 여러 요인에 따라 달라집니다. 2단계의 지속 기간은 몇 초에서 여러 시간까지 다를 수 있습니다.
3. **3단계: (하드 삭제)** "독" 데이터가 있을 수 있는 모든 저장소 아티팩트를 다시 작업하고 저장소에서 삭제합니다. 이 단계는 이전 단계가 완료된 후 최소 5일 *후에* 수행되지만 초기 명령 후 30일을 초과하여 데이터 개인 정보 보호 요구 사항을 준수합니다.

`.purge` 명령을 실행하면 이 프로세스가 트리거되며 완료하는 데 며칠이 걸립니다. 조건어가 적용되는 레코드의 "밀도"가 충분히 큰 경우 프로세스가 테이블의 모든 데이터를 효과적으로 다시 수집하므로 성능 및 COGS에 큰 영향을 미칩니다.


## <a name="purge-limitations-and-considerations"></a>제한 사항 및 고려 사항 제거

* **제거 프로세스는 최종적이고 되돌릴 수 없습니다.** 이 프로세스를 "취소"하거나 제거된 데이터를 복구할 수 없습니다. 따라서 테이블 삭제 [취소와](../management/undo-drop-table-command.md) 같은 명령은 제거된 데이터를 복구할 수 없으며 이전 버전으로 데이터를 롤백하면 최신 제거 명령 "이전"으로 이동할 수 없습니다.

* 실수를 방지하려면 결과가 예상 결과와 일치하는지 확인하기 위해 삭제하기 전에 쿼리를 실행하여 조건어를 확인하거나 제거할 예상 레코드 수를 반환하는 2단계 프로세스를 사용하는 것이 좋습니다. 

* 예방 조치로 는 제거 프로세스는 기본적으로 모든 클러스터에서 비활성화됩니다.
   제거 프로세스를 활성화하는 것은 [지원 티켓을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)열어야 하는 일회성 작업입니다. `EnabledForPurge` 기능을 켜도록 지정하십시오.

* `.purge` 명령은 데이터 관리 끝점에 대해 `https://ingest-[YourClusterName].kusto.windows.net`실행됩니다.
   명령에는 관련 데이터베이스에 대한 [데이터베이스 관리자](../management/access-control/role-based-authorization.md) 권한이 필요합니다. 
* 제거 프로세스 성능 영향 및 [제거 지침이](#purge-guidelines) 준수되도록 하기 위해 호출자는 최소한의 테이블에 관련 데이터 및 테이블당 일괄 처리 명령을 포함하도록 데이터 스키마를 수정하여 제거 프로세스의 상당한 COGS 영향을 줄여야 합니다.
* `predicate` [.purge](#purge-table-tablename-records-command) 명령의 매개 변수는 제거할 레코드를 지정하는 데 사용됩니다.
`Predicate`크기는 63KB로 제한됩니다. 생성 할 `predicate`때 :
    * ['in' 연산자(예:](../query/inoperator.md) `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`.) 사용 
        * ['in' 연산자의](../query/inoperator.md) 한계를 기록합니다(목록은 `1,000,000` 최대 값을 포함할 수 있음).
    * 쿼리 크기가 큰 경우 ['외부 데이터' 연산자(예:](../query/externaldata-operator.md))를 사용합니다. `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` 파일은 제거할 아이디 목록을 저장합니다.
        * 모든 외부 데이터 Blob(모든 Blob의 총 크기)을 확장한 후 총 쿼리 크기는 64MB를 초과할 수 없습니다. 

## <a name="purge-performance"></a>퍼지 성능

클러스터에서 지정된 시간에 하나의 제거 요청만 실행할 수 있습니다. 다른 모든 요청은 "예약된" 상태로 큐에 대기됩니다. 제거 요청 큐 크기를 모니터링하고 데이터에 적용되는 요구 사항에 맞게 적절한 제한 내에서 유지해야 합니다.

제거 실행 시간을 줄이려면 다음을 수행합니다.
* [지우기 지침에](#purge-guidelines) 따라 제거된 데이터 양 줄입니다.
* 콜드 데이터에 대해 제거시간이 더 오래 걸리므로 [캐싱 정책을](../management/cachepolicy.md) 조정합니다.
* 클러스터 확장

* Extents 제거 재구축 용량에 자세히 설명된 대로 신중하게 고려한 후 클러스터 제거 [용량을 늘립니다.](../management/capacitypolicy.md#extents-purge-rebuild-capacity) 이 매개 변수를 변경하려면 지원 티켓을 열어야 [합니다.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>제거 프로세스 트리거

> [!Note]
> 제거 제거 테이블 Table [ *Name* 레코드](#purge-table-tablename-records-command) 명령**https://ingest-([YourClusterName].kusto.windows.net)에서**제거 테이블을 실행 하 여 실행 됩니다.

### <a name="purge-table-tablename-records-command"></a>지우기 테이블 *테이블 이름* 레코드 명령

제거 명령은 서로 다른 사용 시나리오에 대해 두 가지 방법으로 호출될 수 있습니다.
1. 프로그래밍 방식 호출: 응용 프로그램에서 호출할 단일 단계입니다. 이 명령을 직접 호출하면 제거 실행 시퀀스가 트리거됩니다.

    **구문**

     ```kusto
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > [Kusto 클라이언트 라이브러리](../api/netfx/about-kusto-data.md) NuGet 패키지의 일부로 사용할 수 있는 CslCommandGenerator API를 사용하여 이 명령을 생성합니다.

1. 휴먼 호출: 별도의 단계로 명시적 확인이 필요한 2단계 프로세스입니다. 명령의 첫 번째 호출은 실제 제거를 실행하기 위해 제공되어야 하는 확인 토큰을 반환합니다. 이 시퀀스는 실수로 잘못된 데이터를 삭제할 위험을 줄입니다. 이 옵션을 사용하면 콜드 캐시 데이터가 많은 큰 테이블에서 완료하는 데 시간이 오래 걸릴 수 있습니다.
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **구문**

     ```kusto
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |매개 변수  |Description  |
    |---------|---------|
    | DatabaseName   |   데이터베이스 이름      |
    | TableName     |     테이블 이름입니다.    |
    | Predicate    |    제거할 레코드를 식별합니다. 아래의 퍼지 조건자 제한을 참조하십시오. | 
    | 노후회    |     설정된 경우 단일 단계 활성화를 트리거합니다.    |
    | 확인 토큰     |  2단계 활성화**시나리오(noregrets가** 설정되지 않은 경우)에서 이 토큰을 사용하여 두 번째 단계를 실행하고 작업을 커밋할 수 있습니다. **확인 토큰을** 지정하지 않으면 제거에 대한 정보가 반환되고 단계 #2 수행하기 위해 명령으로 다시 전달되어야 하는 명령의 첫 번째 단계가 트리거됩니다.   |

    **조건자 제한 제거**
    * 조건자 (예: *[ColumnName] = =X'* / *여기서 [열 이름] ('X', 'Y', 'Z') 및 [기타 열] == 'A')를*선택해야합니다.
    * 여러 필터는 별도의 `where` 절이 아닌 'and'와 결합되어야 합니다(예: `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` 그렇지 않음).
    * 조건자는 제거되는*테이블(TableName)이*아닌 테이블을 참조할 수 없습니다. 술어는 선택 문 ()만`where`포함할 수 있습니다. 테이블에서 특정 열을 투영할 수 없습니다(실행 시 출력 스키마'* `table` | 조건자*'는 테이블 스키마와 일치해야 합니다.).
    * 시스템 함수(예: `ingestion_time()`및)는 `extent_id()`술어의 일부로 지원되지 않습니다.

#### <a name="example-two-step-purge"></a>예: 2단계 제거

1. 2단계 활성화 시나리오에서 제거를 시작하려면 명령의 단계 #1 실행합니다.

    ```kusto
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **출력**

    |누레코드토퍼지 |예상 퍼지 실행 시간| 확인 토큰
    |--|--|--
    |1,596 |00:00:02 |e43c7184ed22f4f23c7a9a9ad7b124d196be2e570969696987e5badf65057fa65736b

    단계를 실행하기 전에 numRecordsToPurge의 유효성을 검사합니다#2. 
2. 2단계 활성화 시나리오에서 제거를 완료하려면 단계 #1 반환된 확인 토큰을 사용하여 단계 #2 실행합니다.

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **출력**

    |오퍼레이션 ID |DatabaseName |TableName |예약된 시간 |Duration |마지막 업데이트 켜기 |엔진오퍼레이션Id |시스템 상태 |상태 세부 정보 |엔진 시작 시간 |엔진 지속 시간 |다시 시도 |ClientRequestId |주 서버
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e4eadc4 |마이 데이터베이스 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |예약됨 | | | |0 |애. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 ID =...

#### <a name="example-single-step-purge"></a>예: 단일 단계 제거

단일 단계 활성화 시나리오에서 제거를 트리거하려면 다음 명령을 실행합니다.

```kusto
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**출력**

|오퍼레이션 ID |DatabaseName |TableName |예약된 시간 |Duration |마지막 업데이트 켜기 |엔진오퍼레이션Id |시스템 상태 |상태 세부 정보 |엔진 시작 시간 |엔진 지속 시간 |다시 시도 |ClientRequestId |주 서버
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e4eadc4 |마이 데이터베이스 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |예약됨 | | | |0 |애. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 ID =...

### <a name="cancel-purge-operation-command"></a>지우기 작업 명령 취소

필요한 경우 보류 중인 제거 요청을 취소할 수 있습니다.

> [!NOTE]
> 이 작업은 오류 복구 시나리오를 위한 것입니다. 성공을 보장하는 것은 아니며 정상적인 운영 흐름의 일부가 되어서는 안 됩니다. 큐 내 요청에만 적용할 수 있습니다(아직 실행을 위해 엔진 노드로 전달되지 않음). 명령은 데이터 관리 끝점에서 실행됩니다.

**구문**

```kusto
 .cancel purge <OperationId>
 ```

**예제**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**출력**

이 명령의 출력은 취소되는 제거 작업의 업데이트된 상태를 보여주는 '표시 제거 *OperationId'* 명령 출력과 동일합니다. 시도가 성공하면 작업 상태가 '중단'으로 업데이트되고 그렇지 않으면 작업 상태가 변경되지 않습니다. 

|오퍼레이션 ID |DatabaseName |TableName |예약된 시간 |Duration |마지막 업데이트 켜기 |엔진오퍼레이션Id |시스템 상태 |상태 세부 정보 |엔진 시작 시간 |엔진 지속 시간 |다시 시도 |ClientRequestId |주 서버
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e4eadc4 |마이 데이터베이스 |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |애. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 ID =...

## <a name="track-purge-operation-status"></a>지우기 작업 상태 추적 

> [!Note]
> 제거 작업은 데이터 관리 끝점([YourClusterName].kusto.windows.net)에**https://ingest-** 대해 실행되는 [쇼 제거](#show-purges-command) 명령을 사용하여 추적할 수 있습니다.

상태 = '완료됨'은 지우기 작업의 첫 번째 단계가 성공적으로 완료됨을 나타내며, 레코드는 일시 삭제되어 더 이상 쿼리할 수 없습니다. 고객은 두 번째 단계(하드 삭제) 완료를 추적하고 확인할 수 **없습니다.** 이 단계는 Kusto에 의해 내부적으로 모니터링됩니다.

### <a name="show-purges-command"></a>지우기 명령 표시

`Show purges`명령은 요청된 기간 내에 작업 ID를 지정하여 제거 작업 상태를 표시합니다. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|속성  |Description  |필수/옵션
|---------|---------|
|오퍼레이션 ID    |      데이터 관리 작업 ID는 단일 단계 또는 두 번째 단계를 실행한 후 출력됩니다.   |필수
|StartDate    |   필터링 작업의 시간 제한 을 낮춥시입니다. 생략된 경우 기본값은 현재 시간 보다 24시간 전에 설정됩니다.      |옵션
|EndDate    |  필터링 작업에 대한 상한 시간 입니다. 생략된 경우 기본값은 현재 시간으로 설정됩니다.       |옵션
|DatabaseName    |     결과를 필터링할 데이터베이스 이름입니다.    |옵션

> [!NOTE]
> 상태는 클라이언트에 [데이터베이스 관리자](../management/access-control/role-based-authorization.md) 권한이 있는 데이터베이스에만 제공됩니다.

**예**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**출력** 

|오퍼레이션 ID |DatabaseName |TableName |예약된 시간 |Duration |마지막 업데이트 켜기 |엔진오퍼레이션Id |시스템 상태 |상태 세부 정보 |엔진 시작 시간 |엔진 지속 시간 |다시 시도 |ClientRequestId |주 서버
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e4eadc4 |마이 데이터베이스 |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Completed |제거가 성공적으로 완료됨(저장소 아티팩트 삭제 보류 중) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |애. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD 앱 ID =...

* **OperationId** - 제거를 실행할 때 DM 작업 ID가 반환되었습니다. 
* **데이터베이스 이름** - 데이터베이스 이름(대/소문자 구분). 
* **테이블 이름** - 테이블 이름(대/소문자 구분)입니다. 
* **예약시간** - DM 서비스에 대한 퍼지 명령을 실행하는 시간입니다. 
* **기간** - 실행 DM 큐 대기 시간을 포함하여 제거 작업의 총 기간입니다. 
* **EngineOperationId** - 엔진에서 실행중인 실제 제거의 동작 ID입니다. 
* **상태** - 제거 상태는 다음 중 하나가 될 수 있습니다. 
    * 예약됨 - 제거 작업이 실행될 예정입니다. 작업이 **예약된**상태로 유지되는 경우 제거 작업의 백로그가 있을 수 있습니다. 이 백로그를 지우려면 [제거 성능을](#purge-performance) 참조하십시오. 일시적인 오류에서 제거 작업이 실패하면 DM에서 다시 시도되고 **예약된** 것으로 다시 설정됩니다(예약된 작업에서 **Scheduled** **시작률로** 및 **예약된**것으로 다시 작업 전환이 표시될 수 있음).
    * 진행 중 - 제거 작동이 엔진에서 진행 중입니다. 
    * 완료됨 - 제거가 성공적으로 완료되었습니다.
    * BadInput - 잘못된 입력에서 제거가 실패했으며 다시 시도되지 않습니다. 이는 조건자의 구문 오류, 제거 명령에 대한 불법 조건자, 제한을 초과하는 쿼리(예: 외부 데이터 연산자의 1M 엔터티 이상 또는 총 확장된 쿼리 크기 64MB 이상) 및 외부 데이터 Blob에 대한 404 개 또는 403 오류와 같은 다양한 문제 때문일 수 있습니다.
    * 실패 - 제거가 실패하고 다시 시도되지 않습니다. 다른 제거 작업의 백로그 또는 재시도 제한을 초과하는 여러 오류로 인해 작업이 너무 오래(14일 이상) 큐에서 대기중인 경우 이러한 일이 발생할 수 있습니다. 후자는 내부 모니터링 경고를 발생시키고 Azure 데이터 탐색기 팀에서 조사합니다. 
* 상태 세부 정보 - 상태에 대한 설명입니다.
* EngineStartTime - 명령이 엔진에 발행된 시간입니다. 이 시간과 ScheduledTime 간에 큰 차이가 있는 경우 일반적으로 제거 작업의 큰 백로그가 있고 클러스터가 속도를 따라가지 못하고 있습니다. 
* EngineDuration - 엔진에서 실제 퍼지 실행 시간입니다. 제거를 여러 번 다시 시도한 경우 모든 실행 기간의 합계입니다. 
* 재시도 - 일시적인 오류로 인해 DM 서비스에서 작업을 다시 시도한 횟수입니다.
* ClientRequestId - DM 제거 요청의 클라이언트 활동 ID입니다. 
* 보안 주체 - 제거 명령 발급자의 ID입니다.

## <a name="purging-an-entire-table"></a>전체 테이블 제거
테이블을 제거하는 것은 테이블을 삭제하고 [제거 프로세스에](#purge-process) 설명된 하드 삭제 프로세스가 실행되도록 제거된 것으로 표시하는 것을 포함합니다. 테이블을 제거하지 않고 삭제해도 모든 저장소 아티팩트가 삭제되지는 않습니다(테이블에 처음 설정된 하드 보존 정책에 따라 삭제됨). 이 `purge table allrecords` 명령은 빠르고 효율적이며 시나리오에 해당하는 경우 제거 레코드 프로세스보다 훨씬 좋습니다. 

> [!Note]
> 이 명령은 데이터 관리**https://ingest-끝점([YourClusterName].kusto.windows.net)에서** [제거 테이블 *TableName* allrecords](#purge-table-tablename-allrecords-command) 명령을 실행하여 호출됩니다.

### <a name="purge-table-tablename-allrecords-command"></a>지우기 테이블 *테이블이름* allrecords 명령

['.purge 테이블 레코드' ](#purge-table-tablename-records-command)명령과 마찬가지로 이 명령은 프로그래밍 방식(단일 단계) 또는 수동(2단계) 모드에서 호출할 수 있습니다.
1. 프로그래밍 방식 호출(단일 단계):

     **구문**

     ```kusto
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. 인간 호출(2단계):

     **구문**

     ```kusto
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |매개 변수  |Description  |
    |---------|---------|
    |**Databasename**   |   데이터베이스의 이름입니다.      |
    |**Tablename**     |     테이블 이름입니다.    |
    |**노후회**    |     설정된 경우 단일 단계 활성화를 트리거합니다.    |
    |**확인 토큰**     |  2단계 활성화**시나리오(noregrets가** 설정되지 않은 경우)에서 이 토큰을 사용하여 두 번째 단계를 실행하고 작업을 커밋할 수 있습니다. **확인 토큰을** 지정하지 않으면 토큰이 반환되는 명령의 첫 번째 단계를 트리거하여 명령으로 다시 전달하고 #2 명령의 단계를 수행합니다.|

#### <a name="example-two-step-purge"></a>예: 2단계 제거

1. 2단계 활성화 시나리오에서 제거를 시작하려면 명령의 단계 #1 실행합니다. 

    ```kusto
    .purge table MyTable in database MyDatabase allrecords
    ```

    **출력**

    | 확인 토큰
    |--
    |e43c7184ed22f4f23c7a9a9ad7b124d196be2e570969696987e5badf65057fa65736b

1.  2단계 활성화 시나리오에서 제거를 완료하려면 단계 #1 반환된 확인 토큰을 사용하여 단계 #2 실행합니다.

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    출력은 '.show table' 명령 출력(제거된 테이블 없이 반환됨)과 동일합니다.

    **출력**

    |TableName|DatabaseName|폴더|닥스트링 (것)과 함께
    |---|---|---|---
    |기타 테이블|마이 데이터베이스|---|---


#### <a name="example-single-step-purge"></a>예: 단일 단계 제거

단일 단계 활성화 시나리오에서 제거를 트리거하려면 다음 명령을 실행합니다.

```kusto
.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

출력은 '.show table' 명령 출력(제거된 테이블 없이 반환됨)과 동일합니다.

**출력**

|TableName|DatabaseName|폴더|닥스트링 (것)과 함께
|---|---|---|---
|기타 테이블|마이 데이터베이스|---|---


