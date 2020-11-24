---
title: Azure 데이터 탐색기 및 Azure Monitor의 쿼리에 대 한 샘플
description: 이 문서에서는 Azure 데이터 탐색기 및 Azure Monitor 용 Kusto 쿼리 언어를 사용 하는 일반적인 쿼리 및 예제에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783519"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Azure 데이터 탐색기 및 Azure Monitor에 대 한 쿼리에 대 한 샘플

::: zone pivot="azuredataexplorer"

이 문서에서는 Azure 데이터 탐색기의 일반적인 쿼리 요구 사항을 식별 하 고, Kusto 쿼리 언어를 사용 하 여이를 충족 하는 방법을 설명 합니다.

## <a name="display-a-column-chart"></a>세로 막대형 차트 표시

두 개 이상의 열을 프로젝션 한 다음 차트의 x 축 및 y 축으로 열을 사용 하려면 다음을 수행 합니다.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 첫 번째 열은 x 축을 형성 합니다. 숫자, 날짜/시간 또는 문자열일 수 있습니다. 
* `where`, 및를 사용 하 여 `summarize` 표시 되 `top` 는 데이터의 양을 제한 합니다.
* 결과를 정렬 하 여 x 축의 순서를 정의 합니다.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="10 개 위치의 각 값을 나타내는 색이 지정 된 열이 10 개인 세로 막대형 차트의 스크린샷":::

## <a name="get-sessions-from-start-and-stop-events"></a>시작 및 정지 입네트에서 세션 가져오기

이벤트 로그에서 일부 이벤트는 확장 된 작업 또는 세션의 시작 또는 끝을 표시 합니다. 

|Name|City|SessionId|타임스탬프|
|---|---|---|---|
|시작|London|2817330|2015-12-09T10:12:02.32|
|게임|London|2817330|2015-12-09T10:12:52.45|
|시작|맨체스터|4267667|2015-12-09T10:14:02.23|
|중지|London|2817330|2015-12-09T10:23:43.18|
|취소|맨체스터|4267667|2015-12-09T10:27:26.29|
|중지|맨체스터|4267667|2015-12-09T10:28:31.72|

모든 이벤트에는 세션 ID ( `SessionId` )가 있습니다. 시작 및 중지 이벤트와 세션 ID를 일치 시키는 것이 문제입니다.

예제:

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

시작 및 중지 이벤트와 세션 ID를 일치 시키려면 다음을 수행 합니다.

1. Join을 시작 하기 전에 가능한 한 멀리 있는 테이블의 프로젝션 이름을 지정 하려면 [let](./letstatement.md) 을 사용 합니다.
1. [Project](./projectoperator.md) 를 사용 하 여 시작 시간과 중지 시간이 모두 결과에 표시 되도록 타임 스탬프의 이름을 변경 합니다. `project` 또한 결과에서 볼 다른 열을 선택 합니다. 
1. [Join](./joinoperator.md) 을 사용 하 여 동일한 작업에 대 한 시작 및 중지 항목을 일치 시킵니다. 각 활동에 대해 행이 만들어집니다. 
1. 을 다시 사용 하 여 `project` 작업 기간을 표시 하는 열을 추가 합니다.

출력은 다음과 같습니다.

|구/군/시|SessionId|StartTime|StopTime|기간|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|맨체스터|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>세션 ID를 사용 하지 않고 세션 가져오기

시작 및 중지 이벤트에 일치 시킬 수 있는 세션 ID가 없는 것으로 가정 합니다. 그러나 세션이 발생 한 클라이언트의 IP 주소가 있습니다. 각 클라이언트 주소에서 한 번에 하나의 세션만 수행 한다고 가정 하면 동일한 IP 주소에서 각 시작 이벤트를 다음 중지 이벤트에 일치 시킬 수 있습니다.

예제:

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

는 모든 `join` 시작 시간을 동일한 클라이언트 IP 주소의 모든 중지 시간과 일치 시킵니다. 샘플 코드는 다음과 같습니다.

- 이전 중지 시간과 일치 하는 항목을 제거 합니다.
- 각 세션에 대 한 그룹을 가져오는 시작 시간 및 IP 주소를 기준으로 그룹화 합니다. 
- `bin`매개 변수에 대 한 함수를 제공 `StartTime` 합니다. 이 단계를 수행 하지 않는 경우 Kusto는 시작 시간에 잘못 된 중지 시간으로 일치 하는 1 시간 bin을 자동으로 사용 합니다.

`arg_min` 각 그룹에서 기간이 가장 작은 행을 찾고 `*` 매개 변수가 다른 모든 열을 전달 합니다. 

`min_`각 열 이름에 대 한 인수 접두사입니다. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="각 클라이언트/시작 시간 조합에 대 한 시작 시간, 클라이언트 IP, 기간, 도시 및 가장 이른 중지에 대 한 열이 포함 된 결과를 나열 하는 테이블의 스크린샷"::: 

크기를 편리 하 게 만들 수 있도록 코드를 추가 합니다. 이 예에서는 가로 막대형 차트에 대 한 기본 설정으로 인해 시간 범위로를 `1s` 숫자로 변환 하는로 나눕니다.

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="지정 된 범위의 기간이 포함 된 세션 수를 보여 주는 세로 막대형 차트의 스크린샷":::

### <a name="full-example"></a>전체 예제

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>시간 경과에 따른 동시 세션 차트 작성

활동 및 해당 시작 및 종료 시간 테이블이 있다고 가정 합니다. 시간이 지남에 따라 동시에 실행 되는 활동 수를 표시 하는 차트를 표시할 수 있습니다.

다음은 라는 샘플 입력입니다 `X` .

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

1 분 분량의 차트에서는 각 1 분 간격으로 실행 중인 각 활동을 계산 하려고 합니다.

중간 결과는 다음과 같습니다.

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` 지정된 간격으로 값의 배열을 생성합니다.

|SessionId | StartTime | StopTime  | 샘플|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

이러한 배열을 유지 하는 대신 [mv-expand](./mvexpandoperator.md)를 사용 하 여 확장 합니다.

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | 샘플|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

이제 샘플 시간을 기준으로 결과를 그룹화 하 고 각 활동의 발생 횟수를 계산 합니다.

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 을 사용 하 여 `todatetime()` 동적 형식의 열에서 [mv 확장](./mvexpandoperator.md) 결과를 사용 합니다.
* `bin()`숫자 값 및 날짜에 대해 간격을 제공 하지 않을 경우 `summarize` 항상 `bin()` 기본 간격을 사용 하 여 함수를 적용 하기 때문에를 사용 합니다. 

출력은 다음과 같습니다.

| count_SessionId | 샘플|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

가로 막대형 차트 또는 시간 차트를 사용 하 여 결과를 렌더링할 수 있습니다.

## <a name="introduce-null-bins-into-summarize"></a>*요약* 에 null bin 소개

`summarize`날짜-시간 열로 구성 된 그룹 키에 대해 연산자를 적용 하는 경우 해당 값은 고정 너비 bin에 해당 값을 저장 합니다.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

이 예에서는 `T` 5 분의 각 bin에 속하는의 행 그룹당 단일 행이 있는 테이블을 생성 합니다.

코드에서 수행 하는 작업은 "null bin"을 추가 하는 것입니다 .에는 `StartTime` 에 해당 하는 행이 없는 및 사이의 시간 값에 대 한 행이 추가 됩니다 `StopTime` `T` . 이러한 bin을 사용 하 여 테이블을 "패딩" 하는 것이 좋습니다. 이 작업을 수행 하는 한 가지 방법은 다음과 같습니다.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

위의 쿼리는 다음에 대 한 단계별 설명입니다. 

1. 연산자를 사용 `union` 하 여 테이블에 행을 더 추가 합니다. 이러한 행은 식에 의해 생성 됩니다 `union` .
1. `range`연산자는 단일 행과 열이 있는 테이블을 생성 합니다. 이 테이블은가에 대해 `mv-expand` 작업을 수행 하는 데 사용 되지 않습니다.
1. `mv-expand`함수에 대 한 연산자는 `range` 및 사이에 5 분의 계급 만큼의 행을 `StartTime` 만듭니다 `EndTime` .
1. 의를 `Count` 사용 `0` 합니다.
1. `summarize`연산자는 원래 (left 또는 outer) 인수에서로 함께 bin을 그룹화 `union` 합니다. 연산자는 내부 인수 (null bin 행)에도 적용 됩니다. 이 프로세스를 통해 출력에는 값이 0 또는 원래 개수 인 bin 당 하나의 행이 포함 됩니다.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>기계 학습을 통해 Kusto를 사용 하 여 데이터에서 더 많은 정보 얻기 

많은 흥미로운 사용 사례에서 기계 학습 알고리즘을 사용 하 고 원격 분석 데이터에서 흥미로운 정보를 도출 합니다. 이러한 알고리즘은 일반적으로 엄격 하 게 구조화 된 데이터 집합을 입력으로 요구 합니다. 원시 로그 데이터는 일반적으로 필요한 구조와 크기와 일치 하지 않습니다. 

먼저 특정 Bing 추론 서비스에 대 한 오류 발생률의 비정상을 찾습니다. 로그 테이블에는 650억 개의 레코드가 있습니다. 다음 기본 쿼리는 25만 오류를 필터링 한 다음 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)변칙 검색 기능을 사용 하는 일련의 오류를 생성 합니다. 변칙은 Kusto 서비스에 의해 검색 되며 시계열 차트에서 빨간색 점으로 강조 표시 됩니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

서비스에서 의심 스러운 오류 요금이 발생 한 몇 가지 시간 버킷을 식별 했습니다. Kusto를 사용 하 여이 기간을 확대 합니다. 그런 다음 열을 집계 하는 쿼리를 실행 `Message` 합니다. 상위 오류를 찾으려고 시도 합니다. 

메시지에 대 한 전체 스택 추적의 관련 부분이 잘리지 않으므로 페이지에 결과가 더 잘 맞습니다. 

상위 8 개 오류의 성공적인 식별을 확인할 수 있습니다. 그러나 다음은 오류 메시지를 변경 하는 데이터를 포함 하는 서식 문자열을 사용 하 여 오류 메시지를 생성 했기 때문에 발생 합니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|메시지
|---|---
|7125|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|7125|InferenceHostService failed..System를 호출 합니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|7124|예기치 않은 유추 시스템 error..System. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다. 
|5112|예기치 않은 유추 시스템 error..System. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다..
|174|InferenceHostService 호출 failed..System. CommunicationException: 파이프에 쓰는 동안 오류가 발생 했습니다.
|10|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|10|유추 시스템 오류입니다. 추론. UserInterimDataManagerException (...)를 수행 합니다.
|3|InferenceHostService failed..System CommunicationObjectFaultedException: ...
|1|유추 시스템 오류 ... . 상사가 응답을 ... AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD ...
|1|유추 시스템 오류 ... . 상사가 응답을 ... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF ...

이때 연산자를 사용 하면 `reduce` 도움이 됩니다. 연산자는 코드의 동일한 추적 계측 지점에서 발생 하는 63 다른 오류를 식별 했습니다. `reduce` 해당 시간 창에서 의미 있는 추가 오류 추적에 집중할 수 있습니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|개수|패턴
|---|---
|7125|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|  7125|InferenceHostService failed..System를 호출 합니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|  7124|예기치 않은 유추 시스템 error..System. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|  5112|예기치 않은 유추 시스템 error..System. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|  174|InferenceHostService 호출 failed..System. CommunicationException: 파이프에 쓰는 동안 오류가 발생 했습니다.
|  63|유추 시스템 오류입니다. 추론를 \* 작성 하 여 개체를 작성 합니다.: \ m a. \* \* 요청 ...
|  10|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|  10|유추 시스템 오류입니다. 추론. UserInterimDataManagerException (...)를 수행 합니다.
|  3|InferenceHostService failed..System를 호출 합니다. ServiceModel. \* : \* + \* 에 대 한 개체 \* \* , system.servicemodel \* ...... Syst에서 ...

이제 검색 된 변칙에 적용 되는 상위 오류를 볼 수 있습니다.

샘플 시스템에서 이러한 오류의 영향을 이해 하려면 다음을 고려 하십시오. 
* 테이블에는 `Logs` 및와 같은 추가 차원 데이터가 포함 되어 있습니다 `Component` `Cluster` .
* 새 autocluster 플러그 인을 사용 하면 간단한 쿼리로 구성 요소 및 클러스터 정보를 얻을 수 있습니다. 

다음 예에서는 상위 4 개 오류가 구성 요소와 관련 된 것을 명확 하 게 알 수 있습니다. 또한 상위 3 개 오류가 D B 4 클러스터와 관련 되어 있지만 네 번째 오류가 모든 클러스터에서 발생 합니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|개수 |백분율(%)|구성 요소|클러스터|메시지
|---|---|---|---|---
|7125|26.64|InferenceHostService|D B 4|메서드의 ExecuteAlgorithmMethod
|7125|26.64|알 수 없는 구성 요소|D B 4|InferenceHostService 호출 실패 ...
|7124|26.64|InferenceAlgorithmExecutor|D B 4|예기치 않은 유추 시스템 오류입니다.
|5112|19.11|InferenceAlgorithmExecutor|*|예기치 않은 유추 시스템 오류입니다.

## <a name="map-values-from-one-set-to-another"></a>한 집합에서 다른 집합으로 값 매핑

일반적인 쿼리 사용 사례는 값의 정적 매핑입니다. 정적 매핑을 사용 하면 결과를 보다 쉽게 만들 수 있습니다.

예를 들어 다음 표에서는 `DeviceModel` 장치 모델을 지정 합니다. 장치 모델을 사용 하는 것은 장치 이름을 참조 하는 편리한 형식이 아닙니다.  

|DeviceModel |개수 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

 친숙 한 이름을 사용 하는 것이 더 편리 합니다.  

|FriendlyName |개수 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

다음 두 예제에서는 장치 모델을 사용 하 여 장치를 식별 하는 방법을 보여 줍니다.  

### <a name="map-by-using-a-dynamic-dictionary"></a>동적 사전을 사용 하 여 매핑

동적 사전 및 동적 접근자를 사용 하 여 매핑을 구현할 수 있습니다. 예를 들면 다음과 같습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|개수|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>정적 테이블을 사용 하 여 매핑

영구 테이블 및 연산자를 사용 하 여 매핑을 구현할 수도 있습니다 `join` .
 
1. 매핑 테이블을 한 번만 만듭니다.

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. 장치 내용에 대 한 테이블을 만듭니다.

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5, 1 |iPhone 5 
    |iPhone3, 2 |iPhone 4 
    |iPhone7, 2 |iPhone 6 
    |iPhone5, 2 |iPhone5 

1. 테스트 테이블 원본 만들기:

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. 테이블을 조인 하 고 프로젝트를 실행 합니다.

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

출력은 다음과 같습니다.

|FriendlyName |개수 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>쿼리 시간 차원 테이블 만들기 및 사용

종종 쿼리 결과를 데이터베이스에 저장 되지 않은 임시 차원 테이블과 조인 하는 것이 좋습니다. 결과가 단일 쿼리로 범위가 지정 된 테이블인 식을 정의할 수 있습니다. 

예를 들면 다음과 같습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

다음은 약간 더 복잡 한 예제입니다.

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Id 당 최신 레코드 (타임 스탬프 기준)를 검색 합니다.

다음을 포함 하는 테이블이 있다고 가정 합니다.
* `ID`사용자 ID 또는 노드 ID와 같이 각 행이 연결 된 엔터티를 식별 하는 열입니다.
* `timestamp`행에 대 한 시간 참조를 제공 하는 열입니다.
* 기타 열

[Top 중첩 연산자](topnestedoperator.md) 를 사용 하 여 열의 각 값에 대 한 최신 두 레코드를 반환 하는 쿼리를 만들 수 있습니다 `ID` . 여기서 _최신_ 은 _가장 높은 값을 `timestamp` 갖는_ 것으로 정의 됩니다.

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```


앞의 쿼리에 대 한 단계별 설명은 다음과 같습니다 (번호 매기기는 코드 주석의 숫자를 나타냄).

1. 는 `datatable` 데모용으로 일부 테스트 데이터를 생성 하는 방법입니다. 일반적으로 여기에서 실제 데이터를 사용 합니다.
1. 이 줄은 기본적 _으로의 모든 고유 값 `id` 을 반환_ 합니다. 
1. 그런 다음이 줄은 최대화 되는 상위 두 레코드에 대해를 반환 합니다.
     * `timestamp`열
     * 이전 수준의 열 (여기서만 `id` )
     * 이 수준에서 지정 된 열입니다 (여기서는 `timestamp` ).
1. 이 줄은 `bla` 이전 수준에서 반환 된 각 레코드의 열 값을 추가 합니다. 테이블에 관심이 있는 다른 열이 있는 경우 각 열에 대해이 줄을 반복할 수 있습니다.
1. 마지막 줄에서는 [프로젝트 자리 연산자](projectawayoperator.md) 를 사용 하 여에서 도입 된 "추가" 열을 제거 합니다 `top-nested` .

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>합계 계산의 백분율을 기준으로 테이블 확장

숫자 열을 포함 하는 테이블 형식 식은 합계에 대 한 백분율로 값이 함께 제공 되는 경우 사용자에 게 더 유용 합니다.

예를 들어 쿼리가 다음 테이블을 생성 한다고 가정 합니다.

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

다음과 같이 표를 표시 하려고 합니다.

|SomeSeries|SomeInt|.P |
|----------|-------|----|
|Apple       |    100|33.3|
|Banana       |    200|66.6|

테이블이 표시 되는 방식을 변경 하려면 열의 합계 (합계)를 계산한 `SomeInt` 다음이 열의 각 값을 합계로 나눕니다. 임의의 결과에 대해 [as 연산자](asoperator.md)를 사용 합니다.

예를 들면 다음과 같습니다.

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>슬라이딩 윈도우에서 집계 수행

다음 예에서는 슬라이딩 윈도우를 사용 하 여 열을 요약 하는 방법을 보여 줍니다. 쿼리의 경우 과일의 가격이 타임 스탬프로 포함 된 다음 표를 사용 합니다.

7 일 슬라이딩 윈도우를 사용 하 여 하루에 각 과일의 최소, 최대 및 총 비용을 계산 합니다. 결과 집합의 각 레코드는 앞의 7 일을 집계 하며 결과에는 분석 기간에 하루에 대 한 레코드가 포함 됩니다.

과일 테이블:

|타임스탬프|Fruit|가격|
|---|---|---|
|2018-09-24 21:00:00.0000000|바나나|3|
|2018-09-25 20:00:00.0000000|Apples|9|
|2018-09-26 03:00:00.0000000|바나나|4|
|2018-09-27 10:00:00.0000000|Plums|8|
|2018-09-28 07:00:00.0000000|바나나|6|
|2018-09-29 21:00:00.0000000|바나나|8|
|2018-09-30 01:00:00.0000000|Plums|2|
|2018-10-01 05:00:00.0000000|바나나|0|
|2018-10-02 02:00:00.0000000|바나나|0|
|2018-10-03 13:00:00.0000000|Plums|4|
|2018-10-04 14:00:00.0000000|Apples|8|
|2018-10-05 05:00:00.0000000|바나나|2|
|2018-10-06 08:00:00.0000000|Plums|8|
|2018-10-07 12:00:00.0000000|바나나|0|

다음은 슬라이딩 윈도우 집계 쿼리입니다. 쿼리 결과 뒤에 나오는 설명을 참조 하십시오.

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

출력은 다음과 같습니다.

|타임스탬프|Fruit|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|Apples|9|9|9|
|2018-10-01 00:00:00.0000000|바나나|0|8|18|
|2018-10-01 00:00:00.0000000|Plums|2|8|10|
|2018-10-02 00:00:00.0000000|바나나|0|8|18|
|2018-10-02 00:00:00.0000000|Plums|2|8|10|
|2018-10-03 00:00:00.0000000|Plums|2|8|14|
|2018-10-03 00:00:00.0000000|바나나|0|8|14|
|2018-10-04 00:00:00.0000000|바나나|0|8|14|
|2018-10-04 00:00:00.0000000|Plums|2|4|6|
|2018-10-04 00:00:00.0000000|Apples|8|8|8|
|2018-10-05 00:00:00.0000000|바나나|0|8|10|
|2018-10-05 00:00:00.0000000|Plums|2|4|6|
|2018-10-05 00:00:00.0000000|Apples|8|8|8|
|2018-10-06 00:00:00.0000000|Plums|2|8|14|
|2018-10-06 00:00:00.0000000|바나나|0|2|2|
|2018-10-06 00:00:00.0000000|Apples|8|8|8|
|2018-10-07 00:00:00.0000000|바나나|0|2|2|
|2018-10-07 00:00:00.0000000|Plums|4|8|12|
|2018-10-07 00:00:00.0000000|Apples|8|8|8|

쿼리는 입력 테이블의 각 레코드를 실제 모양 보다 7 일 동안 "확장" (중복) 합니다. 각 레코드는 실제로 7 번 표시 됩니다. 결과적으로 일일 집계에는 지난 7 일간의 모든 레코드가 포함 됩니다.


위의 쿼리는 다음에 대 한 단계별 설명입니다. 

1. 각 레코드를 1 일 (기준)으로 기록 `_start` 합니다. 
1. 레코드 마다 범위의 끝을 결정 합니다. `_bin + 7d` 값이 및 범위를 벗어나는 경우를 제외 하 `_start` 고는 크기가 `_end` 조정 됩니다. 
1. 각 레코드에 대해 현재 레코드의 날짜에서 시작 하 여 7 일 (타임 스탬프)의 배열을 만듭니다. 
1. `mv-expand` 따라서 각 레코드를 7 개의 레코드로 복제 하는 것이 서로 떨어져 있는 배열입니다. 
1. 각 날짜에 대 한 집계 함수를 수행 합니다. #4 때문에이 단계는 실제로 _지난_ 7 일을 요약 한 것입니다. 
1. 처음 7 일 동안 7 일 동안의 lookback 기간이 없으므로 처음 7 일 동안의 데이터가 완전 하지 않습니다. 처음 7 일은 최종 결과에서 제외 됩니다. 이 예제에서는 2018-10-01에 대 한 집계에만 참여 합니다.

## <a name="find-the-preceding-event"></a>이전 이벤트 찾기

다음 예제에서는 두 데이터 집합 간의 이전 이벤트를 찾는 방법을 보여 줍니다.  

A와 B 라는 두 개의 데이터 집합이 있습니다. 데이터 집합 B의 각 레코드에 대해 데이터 집합 A에서 이전 이벤트를 찾습니다 (즉, `arg_max` b 보다 _오래_ 된의 레코드).

샘플 데이터 집합은 다음과 같습니다. 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|타임스탬프|ID|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|타임스탬프|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|b|
|2019-01-01 00:00:04.0000000|x|b|
|2019-01-01 00:00:04.0000000|y|b|
|2019-01-01 00:02:00.0000000|z|b|

예상 출력: 

|ID|타임스탬프|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|b|2019-01-01 00:00:00.0000000|Az1|

이 문제에 대 한 두 가지 방법을 권장 합니다. 특정 데이터 집합에 대해 테스트 하 여 시나리오에 가장 적합 한 데이터 집합을 찾을 수 있습니다.

> [!NOTE] 
> 각 접근 방식은 서로 다른 데이터 집합에서 다르게 실행 될 수 있습니다.

### <a name="approach-1"></a>방법 1

이 방법은 두 데이터 집합을 ID와 타임 스탬프로 serialize 합니다. 그런 다음 데이터 집합 B의 모든 이벤트를 데이터 집합 A의 모든 이전 이벤트로 그룹화 합니다. 마지막으로 `arg_max` 그룹의 데이터 집합 A에 있는 모든 이벤트를 선택 합니다.

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="approach-2"></a>방법 2

이 문제를 해결 하는 방법에는 최대 lookback 기간이 필요 합니다. 이 방법은 데이터 집합 A의 레코드를 데이터 집합 B와 비교할 때의 _오래_ 된 정도를 확인 합니다. 그런 다음이 메서드는 ID 및이 lookback 기간을 기준으로 두 데이터 집합을 조인 합니다.

는 모든 `join` 가능한 후보를 생성 하 고, 데이터 집합 B의 레코드 보다 오래 된 레코드의 모든 데이터 집합을 lookback 기간 내에 생성 합니다. 그런 다음 데이터 집합 B에 가장 가까운 것은를 기준으로 필터링 됩니다 `arg_min (TimestampB - TimestampA)` . Lookback 기간이 짧을수록 쿼리 결과가 향상 됩니다.

다음 예제에서 lookback period는로 설정 됩니다 `1m` . ID가 인 레코드는 `z` `A` `A` 이벤트의 이벤트는 2 분이 경과 하므로 해당 이벤트를 포함 하지 않습니다.

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|b|Ay1|
|z|2019-01-01 00:02:00.0000000||b||


## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대 한 자습서](tutorial.md?pivots=azuredataexplorer)를 안내 합니다.

::: zone-end

::: zone pivot="azuremonitor"

이 문서에서는 Azure Monitor의 일반적인 쿼리 요구 사항과이를 충족 하기 위해 Kusto 쿼리 언어를 사용 하는 방법에 대해 설명 합니다.

## <a name="string-operations"></a>문자열 작업

다음 섹션에서는 Kusto 쿼리 언어를 사용할 때 문자열로 작업 하는 방법에 대 한 예제를 제공 합니다.

### <a name="strings-and-how-to-escape-them"></a>문자열 및 이스케이프 하는 방법

문자열 값은 작은따옴표 또는 큰따옴표로 래핑됩니다. 문자 왼쪽에 백슬래시 ()를 추가 \\ 하 여 문자를 이스케이프 합니다. `\t` 예를 들어 tab, `\n` 줄 바꿈 `\"` 문자 및 작은따옴표 문자를 이스케이프 합니다.

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

"\\"가 이스케이프 문자로 사용되지 않도록 하기 위해 문자열의 접두사로 "\@"을 추가합니다.

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>문자열 비교

연산자       |Description                         |대/소문자 구분|예제(`true` 생성)
---------------|------------------------------------|--------------|-----------------------
`==`           |같음                              |Yes           |`"aBc" == "aBc"`
`!=`           |같지 않음                          |Yes           |`"abc" != "ABC"`
`=~`           |같음                              |예            |`"abc" =~ "ABC"`
`!~`           |같지 않음                          |예            |`"aBc" !~ "xyz"`
`has`          |오른쪽 값은 왼쪽 값의 전체 용어입니다. |예|`"North America" has "america"`
`!has`         |오른쪽 값이 왼쪽 값의 전체 용어가 아닙니다.       |예            |`"North America" !has "amer"` 
`has_cs`       |오른쪽 값은 왼쪽 값의 전체 용어입니다. |Yes|`"North America" has_cs "America"`
`!has_cs`      |오른쪽 값이 왼쪽 값의 전체 용어가 아닙니다.       |Yes            |`"North America" !has_cs "amer"` 
`hasprefix`    |오른쪽 값은 왼쪽 값의 용어 접두사입니다.         |예            |`"North America" hasprefix "ame"`
`!hasprefix`   |오른쪽 값이 왼쪽 값의 용어 접두사가 아닙니다.     |예            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |오른쪽 값은 왼쪽 값의 용어 접두사입니다.         |Yes            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |오른쪽 값이 왼쪽 값의 용어 접두사가 아닙니다.     |Yes            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |오른쪽 값은 왼쪽 값의 용어 접미사입니다.         |예            |`"North America" hassuffix "ica"`
`!hassuffix`   |오른쪽 값이 왼쪽 값의 용어 접미사가 아닙니다.     |예            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |오른쪽 값은 왼쪽 값의 용어 접미사입니다.         |Yes            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |오른쪽 값이 왼쪽 값의 용어 접미사가 아닙니다.     |Yes            |`"North America" !hassuffix_cs "icA"`
`contains`     |오른쪽 값이 왼쪽 값의 하위 시퀀스으로 발생 합니다.  |예            |`"FabriKam" contains "BRik"`
`!contains`    |왼쪽 값에서 오른쪽 값이 발생 하지 않습니다.           |예            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |오른쪽 값이 왼쪽 값의 하위 시퀀스으로 발생 합니다.  |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |왼쪽 값에서 오른쪽 값이 발생 하지 않습니다.           |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |오른쪽 값은 왼쪽 값의 초기 하위 시퀀스|예            |`"Fabrikam" startswith "fab"`
`!startswith`  |오른쪽 값이 왼쪽 값의 초기 하위 시퀀스 아닙니다.|예        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |오른쪽 값은 왼쪽 값의 초기 하위 시퀀스|Yes            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |오른쪽 값이 왼쪽 값의 초기 하위 시퀀스 아닙니다.|Yes        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |오른쪽 값은 왼쪽 값의 닫는 하위 시퀀스|예             |`"Fabrikam" endswith "Kam"`
`!endswith`    |오른쪽 값이 왼쪽 값의 닫는 하위 시퀀스 아닙니다.|예         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |오른쪽 값은 왼쪽 값의 닫는 하위 시퀀스|Yes             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |오른쪽 값이 왼쪽 값의 닫는 하위 시퀀스 아닙니다.|Yes         |`"Fabrikam" !endswith "brik"`
`matches regex`|왼쪽 값에 오른쪽 값과 일치 하는 항목이 포함 되어 있습니다.|Yes           |`"Fabrikam" matches regex "b.*k"`
`in`           |요소 중 하나와 같음       |Yes           |`"abc" in ("123", "345", "abc")`
`!in`          |어떤 요소와도 같지 않음   |Yes           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

문자열 내에서 부분 문자열의 발생 횟수를 계산 합니다. 일반 문자열을 찾거나 정규식 (regex)을 사용할 수 있습니다. 일반 문자열 일치는 겹칠 수 있지만 regex 일치는 중복 되지 않습니다.

```
countof(text, search [, kind])
```

- `text`: 입력 문자열 
- `search`: 텍스트 내부와 일치 하는 일반 문자열 또는 regex
- `kind`: _일반_  |  _regex_ (기본값: 보통)입니다.

컨테이너에서 검색 문자열을 일치 시킬 수 있는 횟수를 반환 합니다. 일반 문자열 일치는 겹칠 수 있지만 regex 일치는 중복 되지 않습니다.

#### <a name="plain-string-matches"></a>일반 문자열 일치

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>정규식 일치

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

특정 문자열에서 정규식에 대 한 일치 항목을 가져옵니다. 필요에 따라 추출 된 부분 문자열을 지정 된 형식으로 변환할 수 있습니다.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: 정규식입니다.
- `captureGroup`: 추출할 캡처 그룹을 나타내는 양의 정수 상수입니다. 전체 일치 항목에 대해 0을 사용 하 고, 정규식의 첫 번째 괄호와 일치 하는 값에 대해 1을 사용 \( \) 하 고, 후속 괄호에 대해 2 이상을 사용 합니다.
- `text` -검색할 문자열입니다.
- `typeLiteral` -선택적 형식 리터럴 (예: `typeof(long)` ). 제공된 경우 추출된 부분 문자열이 이 형식으로 변환됩니다.

표시 된 캡처 그룹에 대해 일치 하는 부분 문자열을 반환 합니다 `captureGroup` . 선택적으로로 변환 `typeLiteral` 됩니다. 일치 항목이 없거나 형식 변환이 실패할 경우는 null을 반환 합니다.

다음 예제에서는 `ComputerIP` 하트 비트 레코드에서의 마지막 옥텟을 추출 합니다.

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

다음 예제에서는 마지막 옥텟을 추출 하 고 *실제* 유형 (숫자)으로 캐스팅 한 후 다음 IP 값을 계산 합니다.

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

다음 예제에서는 `Trace` 의 정의에 대 한 문자열을 검색 합니다 `Duration` . 일치 항목은 `real` 시간 상수 (1 s)로 캐스팅 된 다음 `Duration` 형식으로 캐스팅 됩니다 `timespan` .

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*, *isnotempty*, *notempty*

- `isempty``true`인수가 빈 문자열 이거나 null 이면를 반환 하 고,을 참조 하십시오 `isnull` .
- `isnotempty``true`인수가 빈 문자열 또는 null이 아닌 경우를 반환 합니다 (참조 `isnotnull` ). 별칭: `notempty`


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>예제

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*parseurl*

프로토콜, 호스트, 포트 등의 해당 부분으로 URL을 분할 한 다음 부분을 문자열로 포함 하는 사전 개체를 반환 합니다.

```
parseurl(urlstring)
```

#### <a name="example"></a>예제

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

출력은 다음과 같습니다.

```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```

### <a name="replace"></a>*replace*

모든 정규식 일치 항목을 다른 문자열로 바꿉니다. 

```
replace(regex, rewrite, input_text)
```

- `regex`:와 일치 하는 정규식입니다. 괄호 안에 캡처 그룹을 포함할 수 있습니다 \( \) .
- `rewrite`: Regex와 일치 하 여 수행 되는 모든 일치 항목에 대 한 대체 regex입니다. 다음 캡처 그룹의 경우 \ 0을 사용 하 여 첫 번째 캡처 그룹의 경우 \ 1, \ 2 등의 전체 일치 항목을 참조 하십시오.
- `input_text`: 검색할 입력 문자열입니다.

Regex의 모든 일치 항목을 다시 작성 평가로 바꾼 후 텍스트를 반환 합니다. 일치 항목은 겹치지 않습니다.

#### <a name="example"></a>예제

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

출력은 다음과 같습니다.

활동                                        |대체됨
------------------------------------------------|----------------------------------------------------------
4663 - 개체에 액세스하려고 했습니다.  |활동 ID 4663: 개체에 액세스하려고 했습니다.


### <a name="split"></a>*split*

지정 된 구분 기호에 따라 특정 문자열을 분할 한 다음 결과 부분 문자열의 배열을 반환 합니다.

```
split(source, delimiter [, requestedIndex])
```

- `source`: 지정 된 구분 기호에 따라 분할 될 문자열입니다.
- `delimiter`: 소스 문자열을 분할 하는 데 사용 되는 구분 기호입니다.
- `requestedIndex`: 선택적 인덱스 (0부터 시작)입니다. 제공 된 경우 반환 되는 문자열 배열에는 해당 항목만 포함 됩니다 (있는 경우).


#### <a name="example"></a>예제

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

문자열 인수를 연결합니다(1-16개 인수 지원).

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>예제

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

문자열의 길이를 반환합니다.

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>예제

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

지정 된 인덱스에서 시작 하 여 특정 소스 문자열에서 부분 문자열을 추출 합니다. 필요에 따라 요청 된 부분 문자열의 길이를 지정할 수 있습니다.

```
substring(source, startingIndex [, length])
```

- `source`: 부분 문자열이 만들어진 소스 문자열입니다.
- `startingIndex`: 요청 된 부분 문자열의 0부터 시작 하는 문자 위치입니다.
- `length`: 반환 되는 부분 문자열의 요청 된 길이를 지정 하는 데 사용할 수 있는 선택적 매개 변수입니다.

#### <a name="example"></a>예제

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*, *toupper*

특정 문자열을 모두 소문자로 변환 하거나 모두 대문자로 변환 합니다.

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>예제

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>날짜 및 시간 작업

다음 섹션에서는 Kusto 쿼리 언어를 사용 하는 경우 날짜 및 시간 값을 사용 하는 방법에 대 한 예제를 제공 합니다.

### <a name="date-time-basics"></a>날짜-시간 기본 사항

Kusto 쿼리 언어에는 날짜 및 시간과 연결 된 두 가지 기본 데이터 형식인 `datetime` 및가 `timespan` 있습니다. 모든 날짜는 UTC로 표현됩니다. 여러 날짜/시간 형식이 지원 되지만 ISO-8601 형식이 선호 됩니다. 

timespan은 10진수 다음에 시간 단위를 사용해서 표현됩니다.

|약어로   | 시간 단위    |
|:---|:---|
|일           | 일          |
|h           | hour         |
|분           | minute       |
|초           | second       |
|ms          | 밀리초  |
|microsecond | microsecond  |
|tick        | 나노초   |

연산자를 사용 하 여 문자열을 캐스팅 하 여 날짜/시간 값을 만들 수 있습니다 `todatetime` . 예를 들어 특정 기간에 전송 된 VM 하트 비트를 검토 하려면 연산자를 사용 `between` 하 여 시간 범위를 지정 합니다.

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

또 다른 일반적인 시나리오는 날짜-시간 값을 현재와 비교 하는 것입니다. 예를 들어, 지난 2분 동안의 모든 하트비트를 보려면 2분을 나타내는 시간 범위와 `now` 연산자를 함께 사용할 수 있습니다.

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

이러한 함수에 대해 축약형을 사용할 수도 있습니다.

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

가장 짧고 읽기 쉬운 방법은 연산자를 사용 하는 것입니다 `ago` .

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

시작 시간과 종료 시간을 확인 하는 대신 시작 시간과 기간을 알고 있다고 가정 합니다. 다음과 같이 쿼리를 다시 작성할 수 있습니다.

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>시간 단위 변환

기본이 아닌 시간 단위로 날짜/시간 또는 timespan 값을 표현할 수 있습니다. 예를 들어 지난 30 분 동안의 오류 이벤트를 검토 하 고 이벤트 발생 전 시간을 보여 주는 계산 열이 필요한 경우 다음 쿼리를 사용할 수 있습니다.

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

열에는 `timeAgo` `00:09:31.5118992` hh: mm: fffffff 형식으로 지정 된와 같은 값이 포함 됩니다. 이러한 값을 `number` 시작 시간 이후 분의로 서식 지정 하려면 다음으로 해당 값을 나눕니다 `1m` .

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>시간 간격에 따른 집계 및 버킷팅

또 다른 일반적인 시나리오는 특정 시간 단위의 특정 기간에 대 한 통계를 얻는 데 필요 합니다. 이 시나리오에서는 연산자를 절의 일부로 사용할 수 있습니다 `bin` `summarize` .

다음 쿼리를 사용 하 여 지난 30 분 동안 5 분 마다 발생 하는 이벤트 수를 가져옵니다.

```kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

이 쿼리는 다음 테이블을 생성합니다.  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

결과 버킷을 만드는 다른 방법은 다음과 같이 함수를 사용 하는 것입니다 `startofday` .

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

출력은 다음과 같습니다.

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>표준 시간대

모든 날짜-시간 값은 UTC로 표현 되므로 이러한 값을 현지 표준 시간대로 변환 하는 것이 유용한 경우가 많습니다. 예를 들어이 계산을 사용하여 UTC에서 PST 시간으로 변환하려면 다음을 입력합니다.

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>집계

다음 섹션에서는 Kusto 쿼리 언어를 사용할 때 쿼리 결과를 집계 하는 방법에 대 한 예제를 제공 합니다.

### <a name="count"></a>*count*

필터가 적용된 후, 결과 집합의 행 수를 계산합니다. 다음 예에서는 `Perf` 지난 30 분 동안 테이블의 총 행 수를 반환 합니다. `count_`열에 특정 이름을 할당 하지 않으면 이름이 인 열에 결과가 반환 됩니다.


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

시간 차트 시각화는 시간 경과에 따른 추세를 확인 하는 데 유용할 수 있습니다.

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

이 예제의 출력은 `Perf` 5 분 간격의 레코드 수 추세 선을 보여 줍니다.


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="5 분 간격으로 성능 레코드 수 추세 선을 표시 하는 꺾은선형 차트의 스크린샷":::

### <a name="dcount-dcountif"></a>*dcount*, *dcountif*

`dcount` 및 `dcountif`를 사용하여 특정 열의 고유 값을 계산합니다. 다음 쿼리는 지난 시간에 하트비트를 전송한 고유 컴퓨터 수를 평가합니다.

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

하트비트를 전송한 Linux 컴퓨터 수만 계산하려면 `dcountif`를 사용합니다.

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>하위 그룹 평가

데이터의 하위 그룹에 대해 개수 또는 다른 집계를 수행하려면 `by` 키워드를 사용합니다. 예를 들어 각 국가나 지역에서 하트 비트를 보낸 고유 Linux 컴퓨터 수를 계산 하려면 다음 쿼리를 사용 합니다.

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|미국    | 19                  |
|Canada           | 3                   |
|아일랜드          | 0                   |
|United Kingdom   | 0                   |
|네덜란드      | 2                   |


데이터의 더 작은 하위 그룹을 분석 하려면 열 이름을 섹션에 추가 `by` 합니다. 예를 들어 운영 체제 유형별 ()에서 각 국가 또는 지역에 대 한 고유 컴퓨터 수를 계산할 수 있습니다 `OSType` .

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>백분위수

중간 값을 찾으려면 값에 `percentile` 함수를 사용하여 백분위수를 지정합니다.

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

또한 서로 다른 백분위 수을 지정 하 여 집계 결과를 가져올 수 있습니다.

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

결과에 일부 컴퓨터 Cpu의 중앙값이 유사한 것으로 표시 될 수 있습니다. 그러나 일부 컴퓨터는 중앙값을 기반으로 하는 반면 다른 컴퓨터는 훨씬 더 낮은 CPU 값을 보고 했습니다. 높은 값과 낮은 값은 컴퓨터의 스파이크가 발생 했음을 의미 합니다.

### <a name="variance"></a>Variance

값의 분산을 직접 평가하려면 다음과 같이 표준 편차 및 분산 메서드를 사용합니다.

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 사용의 안정성을 분석 하는 좋은 방법은 중앙값과 결합 하는 것입니다 `stdev` .

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>목록 및 집합 생성

`makelist`를 사용 하 여 특정 열에 있는 값의 순서로 데이터를 피벗할 수 있습니다. 예를 들어 컴퓨터에서 발생 하는 가장 일반적인 주문 이벤트를 탐색 하는 것이 좋습니다. 기본적으로 `EventID` 각 컴퓨터에서 값의 순서에 따라 데이터를 피벗할 수 있습니다. 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

출력은 다음과 같습니다.

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`는 데이터가 전달된 순서로 목록을 생성합니다. 이벤트를 가장 오래 된 것에서 최신으로 정렬 하려면 `asc` `order` 대신 문에를 사용 `desc` 합니다. 

고유 값의 목록만 만드는 것이 유용할 수 있습니다. 이 목록을 _집합_ 이라고 하며, 명령을 사용 하 여 생성할 수 있습니다 `makeset` .

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

출력은 다음과 같습니다.

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

와 `makelist` 마찬가지로 `makeset` 도 정렬 된 데이터와 함께 작동 합니다. `makeset`명령은 전달 되는 행의 순서에 따라 배열을 생성 합니다.

### <a name="expand-lists"></a>목록 확장

또는의 역 연산이 `makelist` `makeset` 인 경우 `mv-expand` 이 `mv-expand` 명령은 값 목록을 개별 행으로 확장 합니다. JSON 및 배열 열을 포함 하 여 많은 수의 동적 열에서 확장할 수 있습니다. 예를 들어 `Heartbeat` 지난 시간에 하트 비트를 보낸 컴퓨터에서 데이터를 보낸 솔루션에 대 한 테이블을 확인할 수 있습니다.

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

출력은 다음과 같습니다.

| Computer | 솔루션 | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

`mv-expand`를 사용 하 여 쉼표로 구분 된 목록이 아닌 별도의 행에 각 값을 표시 합니다.

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

출력은 다음과 같습니다.

| Computer | 솔루션 | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


`makelist`항목을 그룹화 하는 데를 사용할 수 있습니다. 출력에서 솔루션 당 컴퓨터 목록을 볼 수 있습니다.

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

출력은 다음과 같습니다.

|솔루션 | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>누락 된 bin

의 유용한 응용 프로그램 `mv-expand` 은 누락 된 bin에 대 한 기본값을 채우고 있습니다. 예를 들어 하트 비트를 탐색 하 여 특정 컴퓨터의 작동 시간을 찾고 있다고 가정 합니다. 또한 열에 있는 하트 비트의 원본을 확인 하려고 합니다 `Category` . 일반적으로 기본 문을 사용 합니다 `summarize` .

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

출력은 다음과 같습니다.

| 범주 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 직접 에이전트 | 2017-06-06T17:00:00Z | 15 |
| 직접 에이전트 | 2017-06-06T18:00:00Z | 60 |
| 직접 에이전트 | 2017-06-06T20:00:00Z | 55 |
| 직접 에이전트 | 2017-06-06T21:00:00Z | 57 |
| 직접 에이전트 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

출력에서 해당 시간에 대 한 하트 비트 데이터가 없으므로 "2017-06-06T19:00:00Z"와 연결 된 버킷이 누락 됩니다. `make-series` 함수를 사용하여 빈 버킷에 기본값을 할당합니다. 각 범주에 대해 행이 생성 됩니다. 출력에는 두 개의 추가 배열 열 (값에 대 한 열과 일치 하는 시간 버킷의 하나)이 포함 됩니다.

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

출력은 다음과 같습니다.

| 범주 | count_ | TimeGenerated |
|---|---|---|
| 직접 에이전트 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*Count_* 배열의 세 번째 요소는 예상 대로 0입니다. _Timegenerated_ 배열의 "2017-06-06T19:00:00.0000000 z"와 일치 하는 타임 스탬프가 있습니다. 그러나이 배열 형식은 읽기가 어렵습니다. `mv-expand`를 사용하여 배열을 확장하고 `summarize`에 의해 생성되는 것과 동일한 형식 출력을 생성합니다.

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

출력은 다음과 같습니다.

| 범주 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 직접 에이전트 | 2017-06-06T17:00:00Z | 15 |
| 직접 에이전트 | 2017-06-06T18:00:00Z | 60 |
| 직접 에이전트 | 2017-06-06T19:00:00Z | 0 |
| 직접 에이전트 | 2017-06-06T20:00:00Z | 55 |
| 직접 에이전트 | 2017-06-06T21:00:00Z | 57 |
| 직접 에이전트 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>요소 집합에 대 한 좁은 결과: *let*, *makeset*, *toscalar*, *in*

일반적인 시나리오는 조건 집합을 기반으로 특정 엔터티의 이름을 선택한 다음 해당 엔터티 집합으로 다른 데이터 집합을 필터링 하는 것입니다. 예를 들어 누락 된 업데이트가 있는 것으로 알려진 컴퓨터와 이러한 컴퓨터에서 호출한 IP 주소를 확인할 수 있습니다.

예를 들면 다음과 같습니다.

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>조인

Join을 사용 하 여 동일한 쿼리에서 여러 테이블의 데이터를 분석할 수 있습니다. 조인은 지정 된 열의 값을 일치 시켜 두 데이터 집합의 행을 병합 합니다.

예를 들면 다음과 같습니다.

```kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

이 예에서 첫 번째 데이터 집합은 모든 로그인 이벤트를 필터링 합니다. 이 데이터 집합은 모든 로그 아웃 이벤트를 필터링 하는 두 번째 데이터 집합에 조인 됩니다. 예상 열은 `Computer` ,, `Account` `TargetLogonId` 및 `TimeGenerated` 입니다. 데이터 집합은 공유 열인와 상관 관계가 `TargetLogonId` 있습니다. 출력은 로그인 및 로그 아웃 시간을 모두 포함 하는 상관 관계 당 단일 레코드입니다.

두 데이터 집합에 이름이 같은 열이 있으면 오른쪽 데이터 집합의 열에 인덱스 번호가 지정 됩니다. 이 예에서는 결과에 `TargetLogonId` 왼쪽 테이블의 값과 오른쪽 테이블의 값이 표시 됩니다 `TargetLogonId1` . 이 경우 두 번째 열은 `TargetLogonId1` 연산자를 사용 하 여 제거 되었습니다 `project-away` .

> [!NOTE]
> 성능을 향상 시키려면 연산자를 사용 하 여 조인 된 데이터 집합의 관련 열만 유지 합니다 `project` .


다음 구문을 사용 하 여 두 테이블 간에 조인 된 키의 이름이 다른 두 데이터 집합을 조인 합니다.

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>조회 테이블

조인의 일반적인 용도는 정적 값 매핑에를 사용 하는 것입니다 `datatable` . `datatable`를 사용 하면 결과를 보다 쉽게 만들 수 있습니다. 예를 들어 각 이벤트 ID에 대 한 이벤트 이름을 사용 하 여 보안 이벤트 데이터를 보강할 수 있습니다.

```kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

출력은 다음과 같습니다.

| eventName | count_ |
|:---|:---|
| 개체에 대 한 핸들이 닫혔습니다. | 290995 |
| 개체에 대 한 핸들이 요청 되었습니다. | 154157 |
| 개체에 대 한 핸들을 복제 하려고 했습니다. | 144305 |
| 개체에 액세스 하려고 했습니다. | 123669 |
| 암호화 작업 | 153495 |
| 키 파일 작업 | 153496 |

## <a name="json-and-data-structures"></a>JSON 및 데이터 구조

중첩 된 개체는 배열 또는 키-값 쌍의 맵에 있는 다른 개체를 포함 하는 개체입니다. 개체는 JSON 문자열로 표시 됩니다. 이 섹션에서는 JSON을 사용 하 여 데이터를 검색 하 고 중첩 된 개체를 분석 하는 방법을 설명 합니다.

### <a name="work-with-json-strings"></a>JSON 문자열 작업

알려진 경로의 특정 JSON 요소에 액세스하려면 `extractjson`을 사용합니다. 이 함수에는 다음 규칙을 사용 하는 경로 식이 필요 합니다.

- _$_ 루트 폴더를 참조 하는 데 사용 합니다.
- 다음 예제와 같이 인덱스 및 요소를 나타내려면 대괄호 또는 점 표기법을 사용합니다.


인덱스에는 대괄호를 사용하고 요소를 구분할 때는 점을 사용합니다.

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

이 예제는 유사 하지만 대괄호 표기법이 사용 됩니다.

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

하나의 요소에 대해서만 점 표기법을 사용할 수 있습니다.

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

JSON 구조의 여러 요소에 동적 개체로 액세스 하는 것이 가장 쉽습니다. 텍스트 데이터를 동적 개체로 캐스팅하려면 `parsejson`을 사용합니다. JSON을 동적 형식으로 변환한 후에는 추가 함수를 사용 하 여 데이터를 분석할 수 있습니다.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

배열 내의 요소 수를 계산하려면 `arraylength`를 사용합니다.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

`mv-expand`를 사용 하 여 개체의 속성을 개별 행으로 나눕니다.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="위치, 상태 및 rate의 값이 포함 된 hosts_0를 보여 주는 스크린샷":::

### <a name="buildschema"></a>*buildschema*

개체의 모든 값을 허용하는 스키마를 가져오려면 `buildschema`를 사용합니다.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

결과는 JSON 형식의 스키마입니다.

```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```

스키마는 개체 필드 이름과 일치 하는 데이터 형식을 설명 합니다. 

중첩 된 개체의 스키마는 다음 예제와 같이 다를 수 있습니다.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>차트

다음 섹션에서는 Kusto 쿼리 언어를 사용할 때 차트를 사용 하는 방법에 대 한 예제를 제공 합니다.

### <a name="chart-the-results"></a>결과 차트

지난 시간 동안 운영 체제별 컴퓨터 수를 검토 하 여 시작 합니다.

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

기본적으로 결과는 테이블로 표시 됩니다.

:::image type="content" source="images/samples/query-results-table.png" alt-text="테이블에 쿼리 결과를 표시 하는 스크린샷":::

보다 유용한 보기를 보려면 **차트** 를 선택 하 고 **원형** 옵션을 선택 하 여 결과를 시각화 합니다.

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="원형 차트에 쿼리 결과를 표시 하는 스크린샷":::

### <a name="timecharts"></a>시간 차트

프로세서 시간의 평균 및 50 번째 및 95 번째 백분위 수를 1 시간의 bin으로 표시 합니다. 

다음 쿼리는 여러 계열을 생성 합니다. 결과에서 시간 차트 표시할 계열을 선택할 수 있습니다.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**꺾은선형** 차트 표시 옵션을 선택 합니다.

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="다중 계열 꺾은선형 차트를 보여 주는 스크린샷":::

#### <a name="reference-line"></a>참조선

참조 줄은 메트릭이 특정 임계값을 초과 하는지 여부를 쉽게 식별 하는 데 도움이 될 수 있습니다. 차트에 선을 추가 하려면 상수 열을 추가 하 여 데이터 집합을 확장 합니다.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="임계값 참조 선을 사용 하는 다중 계열 꺾은선형 차트를 보여 주는 스크린샷":::


### <a name="multiple-dimensions"></a>여러 차원

의 절에 여러 식이 있으면 `by` `summarize` 결과에 여러 행이 생성 됩니다. 값의 각 조합에 대해 하나의 행이 생성 됩니다.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

차트의 결과를 볼 때 차트는 절의 첫 번째 열을 사용 합니다 `by` . 다음 예에서는 값을 사용 하 여 만든 누적 세로 막대형 차트를 보여 줍니다 `EventID` . 차원은 형식 이어야 합니다 `string` . 이 예제에서 값은 `EventID` 로 캐스팅 됩니다 `string` .

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="EventID 열을 기반으로 가로 막대형 차트를 보여 주는 스크린샷":::

열 이름에 대 한 드롭다운 화살표를 선택 하 여 열 사이를 전환할 수 있습니다.

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="열 선택기를 표시 하 여 AccountType 열을 기반으로 가로 막대형 차트를 보여 주는 스크린샷":::

## <a name="smart-analytics"></a>스마트 분석

이 섹션에는 Azure Log Analytics에서 스마트 분석 함수를 사용 하 여 사용자 작업을 분석 하는 예제가 포함 되어 있습니다. 이러한 예제를 사용 하 여 Azure 애플리케이션 Insights에서 모니터링 하는 사용자 고유의 응용 프로그램을 분석 하거나, 이러한 쿼리의 개념을 사용 하 여 다른 데이터와 유사한 분석을 수행할 수 있습니다. 

### <a name="cohorts-analytics"></a>코호트 분석

코 호트 분석에서는 _코 호트_ 이라는 특정 사용자 그룹의 작업을 추적 합니다. 코 호트 analytics는 반환 되는 사용자의 요금을 측정 하 여 서비스의 성능을 측정 하려고 시도 합니다. 사용자는 서비스를 처음 사용 하는 시점에 따라 그룹화 됩니다. 코호트를 분석할 때 첫 번째 추적된 기간에는 활동이 감소할 것으로 예상합니다. 각 코호트에는 해당 멤버가 처음 관찰된 주를 기준으로 이름이 지정됩니다.

다음 예에서는 서비스를 처음 사용한 후 5 주 동안 완료 된 작업 수를 분석 합니다.

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

출력은 다음과 같습니다.

:::image type="content" source="images/samples/cohorts-table.png" alt-text="활동을 기반으로 코 호트의 테이블을 보여 주는 스크린샷":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>롤링 월간 활성 사용자 및 사용자 연결 유지

다음 예에서는 [series_fir](/azure/kusto/query/series-firfunction) 함수를 사용 하 여 시계열 분석을 사용 합니다. `series_fir`슬라이딩 윈도우 계산에 함수를 사용할 수 있습니다. 모니터링되는 샘플 애플리케이션은 사용자 지정 이벤트를 통해 사용자 활동을 추적하는 온라인 상점입니다. 이 쿼리는 두 가지 유형의 사용자 활동 `AddToCart` 및를 `Checkout` 추적 합니다. 특정 날짜에 한 번 이상 체크 아웃을 완료 한 사용자로 활성 사용자를 정의 합니다.

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

출력은 다음과 같습니다.

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="하루에 한 달 동안의 활성 사용자 롤링을 보여 주는 차트의 스크린샷":::

다음 예에서는 앞의 쿼리를 다시 사용할 수 있는 함수로 전환 합니다. 그런 다음 쿼리를 사용 하 여 롤링 사용자의 유지를 계산 합니다. 이 쿼리의 활성 사용자는 특정 날짜에 한 번 이상 체크 아웃을 완료 한 사용자로 정의 됩니다.

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

출력은 다음과 같습니다.

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="시간 경과에 따른 사용자의 사용을 보여 주는 차트의 스크린샷":::

### <a name="regression-analysis"></a>회귀 분석

이 예제에서는 애플리케이션의 추적 로그만 기준으로 해서 서비스 중단의 자동화된 탐지기를 만드는 방법을 보여줍니다. 탐지기는 비정상을 검색 하 여 응용 프로그램에서 발생 하는 오류 및 경고 추적의 상대적 크기를 갑자기 늘립니다.

추적 로그 데이터를 기준으로 서비스 상태를 평가하기 위해 다음 두 가지 기술이 사용됩니다.

- [make-series](/azure/kusto/query/make-seriesoperator)를 사용하여 반구조적 텍스트 추적 로그를 양수 및 음수 추적 라인 간 비율을 나타내는 메트릭으로 변환합니다.
- 두 줄 선형 회귀를 사용 하 여 시계열 분석을 통해 고급 단계 이동 검색에 [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 및 [series_fit_line](/azure/kusto/query/series-fit-linefunction) 를 사용 합니다.

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대 한 자습서](tutorial.md?pivots=azuremonitor)를 안내 합니다.


::: zone-end

