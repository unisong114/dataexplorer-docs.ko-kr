---
title: 연속 데이터 내보내기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 지속적인 데이터 내보내기를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: c5f0d7e9a3fc8daedf55daf4630098af3fb4c07b
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106465"
---
# <a name="continuous-data-export"></a>연속 데이터 내보내기

Kusto에서 [외부 테이블로](../externaltables.md)데이터를 지속적으로 내보냅니다. 외부 테이블은 대상 (예: Azure Blob Storage) 및 내보낸 데이터의 스키마를 정의 합니다. 내보낸 데이터는 정기적으로 쿼리를 실행 하 여 정의 됩니다. 결과는 외부 테이블에 저장 됩니다. 이 프로세스를 통해 모든 레코드를 "정확히 한 번" (모든 레코드를 모든 실행에서 계산 하는 차원 테이블 제외)으로 내보낼 수 있습니다. 

연속 데이터 내보내기를 사용 하려면 [외부 테이블을 만든](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table) 다음 외부 테이블을 가리키는 [연속 내보내기 정의를 만들어야](#create-or-alter-continuous-export) 합니다. 

> [!NOTE] 
> * Kusto는 연속 내보내기의 일부로 연속 내보내기를 만들기 전에 수집 기록 레코드 내보내기를 지원 하지 않습니다. 연속 되지 않은 [내보내기 명령을](export-data-to-an-external-table.md)사용 하 여 기록 레코드를 별도로 내보낼 수 있습니다. 자세한 내용은 [기록 데이터 내보내기](#exporting-historical-data)를 참조 하세요.
> * 연속 내보내기는 스트리밍 수집을 사용 하는 데이터 수집 작동 하지 않습니다. 
> * 현재 [행 수준 보안 정책이](../../management/rowlevelsecuritypolicy.md) 설정 된 테이블에서는 연속 내보내기를 구성할 수 없습니다.
> * 연속 내보내기는 `impersonate` [연결 문자열](../../api/connection-strings/storage.md)에 있는 외부 테이블에 대해서는 지원 되지 않습니다.
> * 연속 내보내기에 사용 되는 아티팩트가 Event Grid 알림을 트리거하기 위한 것 이라면 [Event Grid 설명서의 알려진 문제 섹션](../data-ingestion/eventgrid.md#known-issues)을 참조 하세요.

## <a name="notes"></a>참고

* "정확히 한 번" 내보내기는 [내보낸 아티팩트 표시 명령](#show-continuous-export-artifacts)에 보고 된 파일에 대해서만 보장 됩니다. 
연속 내보내기는 각 레코드가 외부 테이블에 한 번만 기록 되는 것을 보장 하지 않습니다. 내보내기가 시작 된 후에도 오류가 발생 하 고 일부 아티팩트가 이미 외부 테이블에 기록 된 경우에는 외부 테이블에 중복 된 파일이 나 손상 된 파일이 있을 _수 있습니다_ .이 경우 쓰기 작업이 완료 되기 전에 중단 된 경우에도 마찬가지입니다. 이러한 경우 아티팩트는 외부 테이블에서 삭제 되지 않지만 [내보낸 아티팩트 표시 명령](#show-continuous-export-artifacts)에는 보고 *되지* 않습니다. `show exported artifacts command`를 사용 하 여 내보낸 파일 사용 
 중복 되지 않고 손상 되지 않도록 보장 합니다.
* "정확히 한 번" 내보내기를 보장 하기 위해 연속 내보내기는 [데이터베이스 커서](../databasecursor.md)를 사용 합니다. 
[IngestionTime 정책은](../ingestiontime-policy.md) 내보내기에서 "정확히 한 번" 처리 되어야 하는 쿼리에서 참조 되는 모든 테이블에 대해 사용 하도록 설정 해야 합니다. 정책은 새로 만든 모든 테이블에서 기본적으로 사용 하도록 설정 됩니다.
* 내보내기 쿼리의 출력 스키마는 내보낼 외부 테이블의 스키마와 일치 *해야* 합니다. 
* 연속 내보내기는 데이터베이스 간/클러스터 호출을 지원 하지 않습니다.
* 연속 내보내기는 구성 된 기간에 따라 실행 됩니다. 이 간격에 권장 되는 값은 허용 되는 대기 시간에 따라 몇 분 이상입니다. 연속 내보내기는 Kusto에서 지속적으로 데이터를 스트리밍하는 데 적합 *하지 않습니다* . 모든 노드가 동시에 내보내는 분산 된 모드에서 실행 됩니다.
각 실행에서 쿼리 하는 데이터 범위가 작은 경우 연속 내보내기의 출력은 작은 아티팩트 (클러스터의 노드 수에 따라 달라 짐)가 많습니다. 
* 동시에 실행할 수 있는 내보내기 작업 수는 클러스터의 데이터 내보내기 용량에 의해 제한 됩니다 ( [제한](../../management/capacitypolicy.md#throttling)참조). 클러스터에 모든 연속 내보내기를 처리할 충분 한 용량이 없는 경우 일부는 지연 뒤에 시작 됩니다.
* 기본적으로 내보내기 쿼리에서 참조 되는 모든 테이블은 [팩트 테이블로](../../concepts/fact-and-dimension-tables.md)간주 됩니다. 
따라서 데이터베이스 커서의 *범위가* 지정 됩니다. 내보내기 쿼리는 이전 내보내기 실행 이후 조인 된 레코드만 포함 합니다. 
내보내기 쿼리에는 차원 테이블의 *모든* 레코드가 *모든* 내보내기 쿼리에 포함 되는 [차원 테이블이](../../concepts/fact-and-dimension-tables.md) 포함 될 수 있습니다. 
    * 연속-내보내기에서 팩트 테이블과 차원 테이블 간의 조인을 사용 하는 경우 팩트 테이블의 레코드는 한 번만 처리 됩니다. 즉, 일부 키에 대해 차원 테이블의 레코드가 없는 동안 내보내기가 실행 되 면 해당 키에 대 한 레코드가 누락 되거나 내보낸 파일의 차원 열에 대해 null 값이 포함 됩니다 (쿼리에서 내부 또는 외부 조인을 사용 하는지 여부에 따라 다름). 연속 내보내기 정의의 forcedLatency 속성은 이러한 경우에 유용할 수 있습니다. 이러한 경우 팩트 및 차원 테이블이 동일한 시간에 수집 됩니다 (일치 레코드의 경우).
    * 차원 테이블의 연속 내보내기를 지원 하지 않습니다. 내보내기 쿼리는 적어도 하나의 팩트 테이블을 포함 해야 합니다.
    * 이 구문은 범위가 지정 된 테이블 (팩트) 및 범위가 지정 되지 않은 테이블 (차원)을 명시적으로 선언 합니다. 자세한 내용은 `over` [create 명령의](#create-or-alter-continuous-export) 매개 변수를 참조 하세요.
* 내보낸 데이터 볼륨이 큰 경우에는 저장소 제한을 방지 하기 위해 외부 테이블에 대해 여러 저장소 계정을 구성 하는 것이 좋습니다 ( [저장소로 데이터 내보내기](export-data-to-storage.md#known-issues) 문서의 알려진 문제 섹션 참조).
* 최상의 성능을 위해 ADX 클러스터와 저장소 계정은 동일한 Azure 지역에 함께 배치 되어야 합니다.
* 연속 내보내기의 기본 배포는 `per_node` (모든 노드가 동시에 내보내기)입니다. 
  이 설정은 연속 내보내기 만들기 명령의 속성에서 재정의할 수 있습니다. `per_shard`배포를 사용 하 여 동시성을 늘립니다 (저장소 계정의 부하가 증가 하 고 제한 제한에 도달할 수 있음). `single`(또는 `distributed` = `false` )를 사용 하 여 배포를 모두 사용 하지 않도록 설정 합니다 (연속 내보내기 프로세스 속도가 상당히 느려질 수 있음). 이 설정은 각 연속 내보내기 반복에서 생성 되는 파일 수에도 영향을 줍니다. 자세한 내용은 [외부 테이블에 내보내기 명령](export-data-to-an-external-table.md) 에서 참고 섹션을 참조 하세요.
* 각 연속 내보내기 반복에서 내보낸 파일 수는 외부 테이블이 분할 되는 방식에 따라 달라 집니다. 자세한 내용은 [외부 테이블로 내보내기 명령](export-data-to-an-external-table.md)에서 참고 섹션을 참조 하세요.
각 연속 내보내기 반복은 항상 *새* 파일에 쓰고 기존 파일에 추가 하지 않습니다. 결과적으로, 내보낸 파일의 수도 연속 내보내기가 실행 되는 빈도 (매개 변수)에 따라 달라 집니다 `intervalBetweenRuns` .
* 클러스터에서 연속 내보내기의 영향은 대부분의 리소스 (CPU, 메모리)가 쿼리 실행에 사용 되므로 연속 내보내기가 실행 되는 쿼리에 따라 달라 집니다. [명령 및 쿼리 표시 명령을](../commands-and-queries.md) 사용 하 여 리소스 소비를 예측할 수 있습니다. `| where ClientActivityId startswith "RunContinuousExports"`연속 내보내기와 연결 된 명령 및 쿼리를 보려면를 필터링 합니다.


모든 연속 내보내기 명령에는 [데이터베이스 관리자 권한이](../access-control/role-based-authorization.md)필요 합니다.

## <a name="create-or-alter-continuous-export"></a>연속 내보내기 만들기 또는 변경

**구문:**

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)` ] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *쿼리입니다*

**속성**:

| 속성             | 형식     | 설명   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 연속 내보내기의 이름입니다. 이름은 데이터베이스 내에서 고유 해야 하며 주기적으로 연속 내보내기를 실행 하는 데 사용 됩니다.      |
| ExternalTableName    | String   | 내보낼 [외부 테이블](../externaltables.md) 의 이름입니다.  |
| 쿼리                | String   | 내보낼 쿼리입니다.  |
| over (T1, T2)        | String   | 쿼리에서 팩트 테이블의 쉼표로 구분 된 목록 (옵션)입니다. 지정 하지 않으면 쿼리에서 참조 되는 모든 테이블이 팩트 테이블로 간주 됩니다. 지정 된 경우이 목록에 *없는* 테이블은 차원 테이블로 처리 되며 범위가 지정 되지 않습니다. 모든 레코드는 모든 내보내기에 참여 합니다. 자세한 내용은 [참고 섹션](#notes) 을 참조 하세요. |
| 간격 끝점  | Timespan | 연속 내보내기 실행 사이의 시간 범위입니다. 1 분 보다 커야 합니다.   |
| forcedLatency        | Timespan | 이 기간 이전에만 수집 된 레코드로 쿼리를 제한 하는 선택적 기간 (현재 시간 기준)입니다. 이 속성은 예를 들어 쿼리가 일부 집계/조인을 수행 하 고 내보내기를 실행 하기 전에 모든 관련 레코드가 이미 수집 되었는지 확인 하는 경우에 유용 합니다.

위의 외에도 [외부 테이블로 내보내기 명령](export-data-to-an-external-table.md) 에서 지원 되는 모든 속성은 연속 내보내기 만들기 명령에서 지원 됩니다. 

**예:**

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| 이름     | ExternalTableName | 쿼리 | ForcedLatency | 간격 끝점 | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']. ['S '] "<br>] | {<br>  "SizeLimit": 104857600<br>} |

## <a name="show-continuous-export"></a>연속 내보내기 표시

**구문:**

`.show``continuous-export` *ContinuousExportName*

*ContinuousExportName*의 연속 내보내기 속성을 반환 합니다. 

**정보의**

| 속성             | 형식   | 설명                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기의 이름입니다. |


`.show` `continuous-exports`

데이터베이스의 모든 연속 내보내기를 반환 합니다. 

**출력:**

| 출력 매개 변수    | 형식     | 설명                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 명시적으로 범위가 지정 된 (팩트) 테이블 목록 (JSON 직렬화)               |
| ExportProperties    | String   | 속성 내보내기 (JSON 직렬화)                                     |
| ExportedTo          | DateTime | 성공적으로 내보낸 마지막 날짜/시간 (수집 시간)입니다.       |
| ExternalTableName   | String   | 외부 테이블의 이름                                              |
| ForcedLatency       | TimeSpan | 강제 대기 시간 (제공 되지 않은 경우 null)                                   |
| 간격 끝점 | TimeSpan | 실행 간 간격                                                   |
| IsDisabled          | Boolean  | 연속 내보내기를 사용 하지 않도록 설정한 경우 True입니다.                               |
| IsRunning           | Boolean  | 연속 내보내기가 현재 실행 중인 경우 True입니다.                      |
| LastRunResult       | String   | 마지막 연속 내보내기 실행 ( `Completed` 또는)의 결과 `Failed` |
| LastRunTime         | DateTime | 연속 내보내기가 마지막으로 실행 된 시간 (시작 시간)           |
| 이름                | String   | 연속 내보내기의 이름                                           |
| 쿼리               | String   | 내보내기 쿼리                                                            |
| StartCursor         | String   | 이 연속 내보내기에 대 한 첫 번째 실행의 시작 지점         |

## <a name="show-continuous-export-artifacts"></a>연속 내보내기 아티팩트 표시

**구문:**

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

모든 실행에서 연속 내보내기로 내보낸 모든 아티팩트를 반환 합니다. 명령의 타임 스탬프 열을 기준으로 결과를 필터링 하 여 관심 있는 레코드만 볼 수 있습니다. 내보낸 아티팩트의 기록은 14 일 동안 보존 됩니다. 

**정보의**

| 속성             | 형식   | 설명                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기의 이름입니다. |

**출력:**

| 출력 매개 변수  | 형식     | Description                            |
|-------------------|----------|----------------------------------------|
| 타임스탬프         | DateTime | 연속 내보내기 실행의 타임 스탬프 |
| ExternalTableName | String   | 외부 테이블의 이름             |
| 경로              | String   | 출력 경로                            |
| NumRecords        | long     | 경로로 내보낸 레코드 수     |

**예:** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | ExternalTableName | 경로             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |

## <a name="show-continuous-export-failures"></a>연속 내보내기 오류 표시

**구문:**

`.show``continuous-export` *ContinuousExportName*`failures`

연속 내보내기의 일부로 기록 된 모든 오류를 반환 합니다. 명령의 타임 스탬프 열을 기준으로 결과를 필터링 하 여 관심 있는 시간 범위만 볼 수 있습니다. 

**정보의**

| 속성             | 형식   | 설명                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름  |

**출력:**

| 출력 매개 변수 | 형식      | Description                                         |
|------------------|-----------|-----------------------------------------------------|
| 타임스탬프        | DateTime  | 오류의 타임 스탬프입니다.                           |
| OperationId      | String    | 오류의 작업 ID입니다.                    |
| 이름             | String    | 연속 내보내기 이름입니다.                             |
| LastSuccessRun   | Timestamp | 연속 내보내기의 마지막 실행 성공입니다.   |
| FailureKind      | String    | 실패/PartialFailure입니다. PartialFailure는 오류가 발생 하기 전에 일부 아티팩트가 성공적으로 내보내진 것을 나타냅니다. |
| 세부 정보          | String    | 오류 세부 정보입니다.                              |

**예:** 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | 이름     | LastSuccessRun              | FailureKind | 세부 정보    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06:35.6308140 | 실패     | 세부 정보 ... |

## <a name="drop-continuous-export"></a>연속 내보내기 삭제

**구문:**

`.drop``continuous-export` *ContinuousExportName*

**정보의**

| 속성             | 형식   | 설명                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름 |

**출력:**

데이터베이스의 남은 연속 내보내기 (사후 삭제)입니다. [연속 내보내기 표시 명령과](#show-continuous-export)같은 출력 스키마

## <a name="disable-or-enable-continuous-export"></a>연속 내보내기 사용 또는 사용 안 함

**구문:**

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

연속 내보내기 작업을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 비활성화 된 연속 내보내기는 실행 되지 않지만 현재 상태는 유지 되 고 연속 내보내기를 사용 하도록 설정 된 경우 다시 시작할 수 있습니다. 장시간 사용 하지 않도록 설정 된 연속 내보내기를 사용 하도록 설정 하는 경우 내보내기를 사용 하지 않도록 설정할 때 마지막으로 중지 된 위치에서 내보내기가 계속 됩니다. 모든 프로세스를 처리 하기에 충분 한 클러스터 용량이 없는 경우이 연속 작업을 수행 하면 오래 실행 되는 내보내기가 발생할 수 있습니다. 연속 내보내기는 마지막 실행 시간을 기준으로 오름차순으로 실행 됩니다. 즉, 마지막 내보내기가 완료 될 때까지 가장 오래 된 내보내기가 먼저 실행 됩니다. 

**정보의**

| 속성             | 형식   | 설명                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름 |

**출력:**

변경 된 연속 내보내기의 [연속 내보내기 표시 명령](#show-continuous-export) 결과입니다. 




## <a name="exporting-historical-data"></a>기록 데이터 내보내기

연속 내보내기는 생성 시점 에서만 데이터 내보내기를 시작 합니다. 해당 시간 이전에 수집 레코드는 (연속 되지 않은) [내보내기 명령을](export-data-to-an-external-table.md)사용 하 여 별도로 내보내야 합니다. 연속 내보내기로 내보낸 데이터에 중복 되지 않도록 하려면 [연속 내보내기 표시 명령](#show-continuous-export) 및 내보내기 전용 레코드에서 반환 된 startcursor를 사용 하 여 커서 값을 cursor_before_or_at 합니다. 아래 예제를 참조하세요. 기록 데이터가 너무 커서 단일 내보내기 명령으로 내보낼 수 없습니다. 따라서 쿼리를 몇 개의 작은 일괄 처리로 분할 합니다. 

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
