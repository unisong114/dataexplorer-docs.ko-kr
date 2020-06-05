---
title: 샘플-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 샘플을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ffb14b110904bcf94a69d3abeed2fc0b542b0448
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454136"
---
# <a name="samples"></a>샘플

다음은 몇 가지 일반적인 쿼리 요구 사항과이를 충족 하기 위해 Kusto 쿼리 언어를 사용 하는 방법을 보여 줍니다.

## <a name="display-a-column-chart"></a>세로 막대형 차트 표시

두 개 이상의 열을 프로젝션 하 고 차트의 x 축과 y 축으로 사용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 첫 번째 열은 x 축을 형성 합니다. 숫자, 날짜/시간 또는 문자열일 수 있습니다. 
* `where`, 및를 사용 `summarize` 하 여 `top` 표시 되는 데이터의 양을 제한 합니다.
* X 축의 순서를 정의 하기 위해 결과를 정렬 합니다.

:::image type="content" source="images/samples/060.png" alt-text="060":::

## <a name="get-sessions-from-start-and-stop-events"></a>시작 및 정지 입네트에서 세션 가져오기

이벤트 로그가 있고 일부 이벤트가 확장된 활동 또는 세션의 시작 또는 끝을 표시한다고 가정해 보겠습니다. 

|속성|City|SessionId|타임스탬프|
|---|---|---|---|
|시작|London|2817330|2015-12-09T10:12:02.32|
|게임|London|2817330|2015-12-09T10:12:52.45|
|시작|맨체스터|4267667|2015-12-09T10:14:02.23|
|중지|London|2817330|2015-12-09T10:23:43.18|
|취소|맨체스터|4267667|2015-12-09T10:27:26.29|
|중지|맨체스터|4267667|2015-12-09T10:28:31.72|

모든 이벤트에는 세션 Id가 있으므로 문제는 같은 id를 가진 시작 및 중지 이벤트를 일치시키는 것입니다.

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

[`let`](./letstatement.md)을 사용 하 여 조인으로 이동 하기 전에 가능한 한 멀리 떨어져 있는 테이블의 프로젝션 이름을 지정할 수 있습니다.
[`Project`](./projectoperator.md)는 시작 및 중지 시간이 결과에 표시 될 수 있도록 타임 스탬프의 이름을 변경 하는 데 사용 됩니다. 또한 결과에서 보고자 하는 다른 열을 선택합니다. [`join`](./joinoperator.md)각 작업에 대 한 행을 만들어 동일한 작업에 대 한 시작 및 중지 항목을 일치 시킵니다. 마지막으로, `project` 도 역시 활동의 기간을 표시하는 열을 추가합니다.


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|맨체스터|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>세션 id 없이 세션 가져오기

이제 편의상 시작 및 중지 이벤트에 일치시킬 수 있는 세션 ID가 없다고 가정해 보겠습니다. 하지만 세션이 발생한 클라이언트의 IP 주소를 가지고 있습니다. 각 클라이언트 주소가 한 번에 한 세션만 수행한다고 가정하면 각 시작 이벤트를 같은 IP 주소의 다음 중지 이벤트에 일치시킬 수 있습니다.

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

조인은 모든 시작 시간을 같은 클라이언트 IP 주소의 모든 중지 시간과 일치시킵니다. 따라서 더 이전의 중지 시간과 일치하는 항목을 먼저 제거합니다.

그런 다음 시작 시간 및 ip를 기준으로 그룹화 하 여 각 세션에 대 한 그룹을 가져옵니다. `bin`StartTime 매개 변수에 대 한 함수를 제공 해야 합니다. 그렇지 않으면 Kusto가 자동으로 1 시간 bin을 사용 하 여 시작 시간에 잘못 된 중지 시간을 일치 시킵니다.

`arg_min`각 그룹에서 가장 작은 기간을 사용 하 여 행을 선택 하 고 `*` , 매개 변수는 각 열 이름에 "min_"을 접두사로 사용 하지만 다른 모든 열을 통과 합니다. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

그런 다음 코드를 추가 하 여 크기를 편리 하 게 만들 수 있습니다. 가로 막대형 차트에는 약간의 기본 설정이 있으므로 `1s` 시간 범위로를 숫자로 변환 합니다. 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

### <a name="real-example"></a>실제 예제

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

시작 및 종료 시간이 포함된 활동의 테이블이 있다고 가정합니다.  임의 시기에 동시에 실행할 수 있는 양을 표시하는 시간 경과에 따른 차트를 확인하려고 합니다.

다음은 예제 입력이며, 이를 편의상 `X`이라고 부르겠습니다.

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

1분 bin의 차트를 원하므로 매 1m 간격으로 실행 중인 각 활동에 대해 계산할 수 있는 무언가를 만들어야 합니다.

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

그러나 이러한 배열을 유지 하는 대신 [mv 확장](./mvexpandoperator.md)을 사용 하 여 확장 합니다.

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

이제 샘플 시간을 기준으로 이들을 그룹화하여 각 활동의 동시성을 계산할 수 있습니다.

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* [Mv 확장](./mvexpandoperator.md) 은 동적 형식의 열을 생성 하기 때문에 system.xml.xmlconvert.todatetime ()가 필요 합니다.
* 숫자 값 및 날짜의 경우 summarize는 간격을 제공하지 않으면 언제나 기본 간격을 가진 bin 함수를 적용하므로 bin()이 필요합니다. 


| count_SessionId | 샘플|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

이 정보를 가로 막대형 차트 또는 시간 차트로 렌더링할 수 있습니다.

## <a name="introduce-null-bins-into-summarize"></a>요약에 null bin 소개

`summarize`열로 구성 된 그룹 키에 대해 연산자를 적용 하는 경우이 연산자는 `datetime` 일반적으로 고정 폭 bin에 대 한 값을 "bin"으로 만듭니다. 예를 들어:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

이 작업을 수행 하면 `T` 5 분의 각 bin에 속하는의 행 그룹당 하나의 행이 있는 테이블이 생성 됩니다. 수행 하지 않는 작업은 "null bin"을 추가 하는 것입니다 `StartTime` `StopTime` .에는에 해당 하는 행이 없는 및 사이의 시간 값에 대 한 행이 추가 됩니다 `T` . 

이러한 bin을 사용 하 여 테이블을 "패딩" 하는 것이 좋습니다. 이 작업을 수행 하는 한 가지 방법은 다음과 같습니다.

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

위의 쿼리에 대 한 단계별 설명은 다음과 같습니다. 

1. 연산자를 사용 하 여 `union` 테이블에 행을 더 추가할 수 있습니다. 이러한 행은 식에서로 생성 됩니다 `union` .
2. 연산자를 사용 하 여 `range` 단일 행/열이 있는 테이블을 생성 합니다.
   이 테이블은 `mv-expand` 에서 작업을 수행 하는 데 사용 되지 않습니다.
3. 함수에 연산자를 사용 하 여 `mv-expand` `range` 및 사이에 5 분의 계급 수 만큼의 행을 `StartTime` 만듭니다 `EndTime` .
4. 가 인 모든 `Count` `0` 입니다.
5. 마지막으로 연산자를 사용 `summarize` 하 여 원래 (왼쪽 또는 외부) 인수에서로 그룹을 그룹화 하 `union` 고 내부 인수 (즉, null bin 행)에서 bin으로 묶습니다. 이렇게 하면 출력에 bin 당 하나의 행이 포함 되 고 해당 값은 0 또는 원래 개수가 됩니다.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Machine Learning를 사용 하 여 Kusto 데이터 가져오기 

기계 학습 알고리즘을 활용 하 고 원격 분석 데이터에서 흥미로운 정보를 얻을 수 있는 많은 흥미로운 사용 사례가 있습니다. 이러한 알고리즘은 매우 구조화 된 데이터 집합을 입력으로 필요로 하지만 원시 로그 데이터는 일반적으로 필요한 구조와 크기와 일치 하지 않습니다. 

Microsoft의 경험을 통해 특정 Bing 추론 서비스의 오류 요금에 대 한 변칙을 찾습니다. Logs 테이블에는 65B 레코드가 있으며 아래의 간단한 쿼리는 250K 오류를 필터링 하 고 변칙 검색 기능 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)를 활용 하는 오류 수의 시계열 데이터를 만듭니다. 변칙은 Kusto 서비스에 의해 검색 되며 시계열 차트에서 빨간색 점으로 강조 표시 됩니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

서비스가 의심 스러운 오류 발생률의 몇 가지 시간 버킷을 식별 했습니다. Kusto를 사용 하 여이 시간 프레임을 확대 하 고, 상위 오류를 찾으려고 시도 하는 ' Message ' 열에서 집계 하는 쿼리를 실행 합니다. 페이지에 더 잘 맞도록 메시지의 전체 스택 추적에서 관련 된 파트를 잘라냅니다. 상위 8 개 오류를 사용 하는 것이 좋지만, 오류 메시지는 변경 데이터를 포함 하는 형식 문자열에 의해 생성 되었으므로 긴 오류에 도달 했음을 알 수 있습니다. 

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
|7125|InferenceHostService 호출에 실패 했습니다.. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|7124|예기치 않은 유추 시스템 오류입니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다. 
|5112|예기치 않은 유추 시스템 오류입니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다..
|174|InferenceHostService를 호출 하지 못했습니다. CommunicationException: 파이프에 쓰는 동안 오류가 발생 했습니다.
|10|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|10|유추 시스템 오류입니다. 추론. UserInterimDataManagerException (...)를 수행 합니다.
|3|InferenceHostService를 호출 하지 못했습니다. CommunicationObjectFaultedException: ...
|1|유추 시스템 오류 ... . 상사가 응답을 ... AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD ...
|1|유추 시스템 오류 ... . 상사가 응답을 ... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF ...

여기서는 연산자가 도움이 될 것입니다 `reduce` . `reduce`연산자는 코드에서 동일한 추적 계측 지점에 의해 발생 한 것과 다른 오류를 63으로 식별 하 고, 해당 기간에 의미 있는 추가 오류 추적에 초점을 맞춘 데 도움을 주었습니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|개수|무늬
|---|---
|7125|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|  7125|InferenceHostService 호출에 실패 했습니다.. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|  7124|예기치 않은 유추 시스템 오류입니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
|  5112|예기치 않은 유추 시스템 오류입니다. NullReferenceException: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다..
|  174|InferenceHostService를 호출 하지 못했습니다. CommunicationException: 파이프에 쓰는 동안 오류가 발생 했습니다.
|  63|유추 시스템 오류입니다. 추론를 \* 작성 하 여 개체를 작성 합니다.: \ m a. \* \* 요청 ...
|  10|' RunCycleFromInterimData ' 메서드에 대 한 ExecuteAlgorithmMethod가 실패 했습니다.
|  10|유추 시스템 오류입니다. 추론. UserInterimDataManagerException (...)를 수행 합니다.
|  3|InferenceHostService 호출에 실패 했습니다.. System.servicemodel. \* : \* + \* 에 대 한 개체, \* \* system.servicemodel..... \*   Syst에서 ...

검색 된 이상에 영향을 주는 상위 오류에 대 한 좋은 뷰가 있으므로 시스템에서 이러한 오류의 영향을 이해 하고자 합니다. ' Logs ' 테이블에는 ' Component ', ' Cluster ' 등의 추가 차원 데이터가 포함 되어 있습니다. 새 ' autocluster ' 플러그 인은 간단한 쿼리를 사용 하 여 해당 정보를 도출 하는 데 도움이 될 수 있습니다. 아래 예제에서는 상위 4 개 오류가 구성 요소에만 해당 되는 것을 명확 하 게 알 수 있으며, 상위 3 개 오류가 D B 4 클러스터에만 해당 되는 경우에는 네 번째 오류가 모든 클러스터에서 발생 합니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|개수 |백분율(%)|구성 요소|클러스터|메시지
|---|---|---|---|---
|7125|26.64|InferenceHostService|D B 4|ExecuteAlgorithmMethod for 메서드 ....
|7125|26.64|알 수 없는 구성 요소|D B 4|InferenceHostService 호출 실패 ....
|7124|26.64|InferenceAlgorithmExecutor|D B 4|예기치 않은 유추 시스템 오류입니다.
|5112|19.11|InferenceAlgorithmExecutor|*|예기치 않은 유추 시스템 오류입니다. 

## <a name="mapping-values-from-one-set-to-another"></a>한 집합에서 다른 집합으로 값 매핑

일반적인 사용 사례는 결과를 더 편리 하 게 활용할 수 있도록 하는 값의 정적 매핑을 사용 하는 것입니다.  
예를 들어 다음 테이블을 생각해 보겠습니다. DeviceModel는 장치 이름에 대 한 참조의 매우 편리한 양식이 아닌 장치 모델을 지정 합니다.  


|DeviceModel |개수 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
더 나은 표현은 다음과 같습니다.  

|FriendlyName |개수 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

아래 두 가지 방법으로이를 달성할 수 있는 방법을 보여 줍니다.  

### <a name="mapping-using-dynamic-dictionary"></a>동적 사전을 사용 하 여 매핑

다음 방법에서는 동적 사전 및 동적 접근자를 사용 하 여 매핑을 구현할 수 있는 방법을 보여 줍니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Data set definition
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



### <a name="mapping-using-static-table"></a>정적 테이블을 사용 하 여 매핑

다음 방법에서는 영구 테이블 및 조인 연산자를 사용 하 여 매핑을 구현할 수 있는 방법을 보여 줍니다.
 
매핑 테이블을 한 번만 만듭니다.

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

이제 장치 내용:

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 


테스트 테이블 원본을 만드는 것과 동일한 트릭:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


조인 및 프로젝트:

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

결과:

|FriendlyName |개수 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>쿼리 시간 차원 테이블 만들기 및 사용

대부분의 경우 데이터베이스에 저장 되지 않은 일부 임시 차원 테이블과 쿼리 결과를 조인 하려고 합니다. 다음과 같은 작업을 수행 하 여 결과를 단일 쿼리로 범위가 지정 된 테이블로 지정 하는 식을 정의할 수 있습니다.

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>Id 당 최신 (타임 스탬프) 레코드 검색

`id`열 (사용자 Id 또는 노드 Id와 같이 각 행이 연결 된 엔터티 식별) 및 `timestamp` 열 (행에 대 한 시간 참조 제공) 및 기타 열을 포함 하는 테이블이 있다고 가정 합니다. 목표는 열의 각 값에 대 한 최신 2 개의 레코드를 반환 하는 쿼리를 작성 하는 것입니다 `id` . 여기서 "최신"은 "가장 높은 값을 갖는"으로 정의 됩니다 `timestamp` .

이 작업은 [top 중첩 연산자](topnestedoperator.md)를 사용 하 여 수행할 수 있습니다.
먼저 쿼리를 제공한 다음 설명 하겠습니다.

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

메모
1. 은 `datatable` 데모용으로 일부 테스트 데이터를 생성 하는 방법일 뿐입니다. 물론 실제로 여기에 데이터를 포함 합니다.
2. 이 줄은 기본적으로 "모든 고유 값 반환 `id` "을 의미 합니다.
3. 그런 다음이 줄은 열을 최대화 하는 상위 2 개 레코드에 대해, `timestamp` 이전 수준의 열 (여기서는 `id` ) 및이 수준에서 지정 된 열 (여기서는)을 반환 합니다 `timestamp` .
4. 이 줄은 `bla` 이전 수준에서 반환 된 각 레코드의 열 값을 추가 합니다. 테이블에 다른 관련 열이 있으면 해당 열 마다이 줄이 반복 됩니다.
5. 마지막으로, [프로젝트 자리 연산자](projectawayoperator.md) 를 사용 하 여에서 도입 된 "추가" 열을 제거 `top-nested` 합니다.

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>일부 합계 계산으로 테이블 확장

종종 숫자 열을 포함 하는 테이블 형식 식이 있는 경우 해당 열을 사용자에 게 해당 열을 합계의 백분율로 표시 하는 것이 desireable 됩니다. 예를 들어 다음 테이블에 해당 하는 값을 가진 쿼리가 있다고 가정 합니다.

|SomeSeries|SomeInt|
|----------|-------|
|Foo       |    100|
|막대형       |    200|

이 테이블을 다음과 같이 표시 하려고 합니다.

|SomeSeries|SomeInt|.P |
|----------|-------|----|
|Foo       |    100|33.3|
|막대형       |    200|66.6|

이렇게 하려면 열의 합계 (합계)를 계산한 `SomeInt` 다음이 열의 각 값을 합계로 나눕니다. [As 연산자](asoperator.md)를 사용 하 여 이러한 결과에 이름을 지정 하면 임의의 결과에 대해이 작업을 수행할 수 있습니다.

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="performing-aggregations-over-a-sliding-window"></a>슬라이딩 윈도우에서 집계 수행
다음 예에서는 슬라이딩 윈도우를 사용 하 여 열을 요약 하는 방법을 보여 줍니다. 타임 스탬프가 과일 가격이 포함 된 아래 표를 예로 들어 보겠습니다. 30 일의 슬라이딩 윈도우를 사용 하 여 일별 각 과일의 최소, 최대 및 총 비용을 계산 하려고 한다고 가정 합니다. 즉, 결과 집합의 각 레코드는 지난 7 일을 집계 하 고 결과에는 분석 기간의 하루에 대 한 레코드가 포함 됩니다.  

과일 테이블: 

|타임스탬프|Fruit|Price|
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

슬라이딩 윈도우 집계 쿼리 (설명이 쿼리 결과 아래에 제공 됨): 

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

쿼리 세부 정보: 

쿼리가 "확장" (중복) 하 여 7 일 동안 입력 테이블의 각 레코드를 실제 모양으로 게시 하 여 각 레코드가 실제로 7 번 표시 되도록 합니다. 결과적으로 매일 집계를 수행할 때 집계에는 지난 7 일간의 모든 레코드가 포함 됩니다.

단계별 설명 (숫자는 쿼리 인라인 주석에서 숫자를 참조):
1. Bin은 각 레코드를 1d (_start 기준)에 기록 합니다. 
2. _(시작, 끝)_ 범위를 벗어나는 경우를 제외 하 고 레코드 _bin + 7d 범위의 끝을 결정 합니다 .이 경우에는이 범위가 조정 됩니다. 
3. 각 레코드에 대해 현재 레코드의 날짜에서 시작 하 여 7 일 (타임 스탬프)의 배열을 만듭니다. 
4. m-배열을 확장 하 여 각 레코드를 7 개의 레코드로 복제 합니다. 즉, 1 일은 서로 떨어져 있습니다. 
5. 각 날짜에 대 한 집계 함수를 수행 합니다. #4로 인해 실제로는 _지난_ 7 일간 요약 합니다. 
6. 마지막으로, 첫 번째 7d의 데이터가 불완전 하기 때문에 (처음 7 일 동안에는 7d lookback 기간이 없음) 최종 결과에서 처음 7 일을 제외 합니다 (2018-10-01에 대 한 집계에만 참여). 

## <a name="find-preceding-event"></a>이전 이벤트 찾기
다음 예에서는 두 데이터 집합 간의 이전 이벤트를 찾는 방법을 보여 줍니다.  

*목적:* : 지정 된 2 개의 데이터 집합, a와 B는 b의 각 레코드에 대해 이전 이벤트를 찾습니다 (즉,의 arg_max 레코드는 b 보다 "이전"). 예를 들어 다음 예제 데이터 집합에 대 한 예상 출력은 다음과 같습니다. 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|타임스탬프|Id|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|타임스탬프|Id|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|b|
|2019-01-01 00:00:04.0000000|x|b|
|2019-01-01 00:00:04.0000000|y|b|
|2019-01-01 00:02:00.0000000|z|b|

예상 출력: 

|Id|타임스탬프|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|b|2019-01-01 00:00:00.0000000|Az1|

이 문제에 대 한 두 가지 방법이 제안 됩니다. 특정 데이터 집합에 대해 테스트 하 여 가장 적합 한 데이터 집합을 찾아야 합니다 (다른 데이터 집합에서 다르게 수행 될 수 있음). 

### <a name="suggestion-1"></a>제안 #1:
이 제안에서는 Id와 타임 스탬프를 사용 하 여 두 데이터 집합을 serialize 한 다음 모든 B 이벤트를 앞의 이벤트와 함께 그룹화 하 고 그룹에서를 모두 선택 합니다 `arg_max` . 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>제안 #2:
이 제안에는 최대 lookback 기간 ()을 정의 해야 합니다. 즉, A의 레코드를 B?와 비교할 수 있도록 허용 하는 정도를 정의한 다음 Id 및이 lookback에 있는 2 개의 데이터 집합을 조인 합니다. 조인은 가능한 모든 후보 (B 보다 오래 된 모든 A 레코드 및 lookback 기간 내에 있음)를 생성 한 다음 가장 가까운 것을 arg_min (TimestampB – TimestampA)를 통해 B로 필터링 합니다. Lookback 기간이 작을수록 쿼리가 더 잘 수행 될 것으로 예상 됩니다. 

아래 예제에서 lookback period는 1m로 설정 되어 있으므로 Id가 ' z ' 인 레코드에는 해당 하는 ' A ' 이벤트가 없습니다 .이 이벤트는 ' A '가 2m에 의해 오래 되었기 때문입니다.

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
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|b|Ay1|
|z|2019-01-01 00:02:00.0000000||b||
