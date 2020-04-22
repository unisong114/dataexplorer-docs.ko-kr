---
title: 샘플 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 샘플에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663586"
---
# <a name="samples"></a>샘플

다음은 몇 가지 일반적인 쿼리 요구 사항과 Kusto 쿼리 언어를 사용하여 이를 충족하는 방법입니다.

## <a name="display-a-column-chart"></a>열차트 표시

두 개 이상의 열을 투영하고 차트의 x 및 y 축으로 사용합니다.

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 첫 번째 열은 x축을 형성합니다. 숫자, 날짜 시간 또는 문자열일 수 있습니다. 
* 을 `where` `summarize` 사용하고 `top` 표시하는 데이터의 볼륨을 제한합니다.
* x축의 순서를 정의할 수 있도록 결과를 정렬합니다.

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>시작 및 정지 입네트에서 세션 가져오기

이벤트 로그가 있고 일부 이벤트가 확장된 활동 또는 세션의 시작 또는 끝을 표시한다고 가정해 보겠습니다. 

|이름|City|SessionId|타임스탬프|
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

조인으로 들어가기 전에 가능한 한 아래로 구배된 테이블의 투영 이름을 지정하는 데 사용합니다. [`let`](./letstatement.md)
[`Project`](./projectoperator.md)는 시작 시간과 중지 시간이 모두 결과에 표시될 수 있도록 타임스탬프의 이름을 변경하는 데 사용됩니다. 또한 결과에서 보고자 하는 다른 열을 선택합니다. [`join`](./joinoperator.md)은 동일한 활동에 대한 시작 및 중지 항목을 일치시켜 각 활동에 대한 행을 만듭니다. 마지막으로, `project` 도 역시 활동의 기간을 표시하는 열을 추가합니다.


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

그런 다음 시작 시간과 IP별로 그룹화하여 각 세션에 대한 그룹을 얻습니다. 우리는 StartTime `bin` 매개 변수에 대한 함수를 제공해야합니다 : 우리가하지 않으면 Kusto는 자동으로 잘못된 중지 시간과 일부 시작 시간과 일치하는 1 시간 저장소를 사용합니다.

`arg_min`각 그룹에서 가장 작은 기간으로 행을 `*` 선택하고 매개 변수는 각 열 이름에 "min_"를 접두사로 지정하지만 다른 모든 열을 통과합니다. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

그런 다음 몇 가지 코드를 추가하여 편리한 크기의 저장소에서 기간을 계산할 수 있습니다. 가로 막대형 차트에 대한 선호도가 약간 `1s` 있으므로 시간 범위를 숫자로 변환하는 것으로 나뉩니다. 


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

<a name="concurrent-activities"><a/>
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

그러나 이러한 배열을 유지하는 대신 [mv 확장을](./mvexpandoperator.md)사용하여 배열을 확장합니다.

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

* [mv 확장은](./mvexpandoperator.md) 동적 형식의 열을 생성하기 때문에 todatetime()이 필요합니다.
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

## <a name="introduce-null-bins-into-summarize"></a>요약에 null 저장소 소개

`datetime` 열로 `summarize` 구성된 그룹 키위에 연산자가 적용되면 일반적으로 해당 값을 고정 너비 저장소에 "bin"합니다. 예를 들어:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

이 작업은 5분의 각 저장소에 속하는 행 `T` 그룹당 단일 행이 있는 테이블을 생성합니다. 하지 않는 것은 `StartTime` `StopTime` `T`"null bins"를 추가하는 것입니다 . 

종종, 그 쓰레기통 테이블을 "패드"하는 것이 좋습니다. 한 가지 방법은 다음과 같습니다.

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

위의 쿼리에 대한 단계별 설명은 다음과 같습니다. 

1. 연산자 사용을 `union` 사용하면 테이블에 행을 추가할 수 있습니다. 이러한 행은 `union`에 대한 식에 의해 생성됩니다.
2. 연산자사용으로 `range` 단일 행/열이 있는 테이블을 생성합니다.
   이 테이블은 작업 이외의 `mv-expand` 작업에는 사용되지 않습니다.
3. 함수를 `mv-expand` 통해 연산자를 사용하여 `EndTime`5분 사이의 저장소와. `StartTime` `range`
4. 모두 의 `Count` `0`.
5. 마지막으로 `summarize` 연산자는 원래(왼쪽 또는 바깥쪽) 인수에서 내부 인수(즉, null bin 행)로 bin을 `union` 그룹화하고 내부 인수에서 bin으로 묶습니다. 이렇게 하면 출력에 bin당 하나의 행이 있으며, 그 값은 0 또는 원래 개수입니다.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>기계 학습을 사용하여 Kusto에서 데이터를 더 많이 활용 

기계 학습 알고리즘을 활용하고 원격 분석 데이터에서 흥미로운 통찰력을 도출하기 위한 흥미로운 사용 사례가 많이 있습니다. 이러한 알고리즘은 입력으로 매우 구조화 된 데이터 집합을 필요로 하는 경우가 많지만 원시 로그 데이터는 일반적으로 필요한 구조 및 크기와 일치하지 않습니다. 

우리의 여행은 특정 빙 추론 서비스의 오류 율에 이상을 찾는 것으로 시작합니다. Logs 테이블에는 65B 레코드가 있으며 아래의 간단한 쿼리는 250K 오류를 필터링하고 변칙 검색 기능을 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)사용하는 오류 수의 시계열 데이터를 만듭니다. 이상 징후는 Kusto 서비스에서 감지하고 타임 시리즈 차트에서 빨간색 점으로 강조 표시됩니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

이 서비스는 의심스러운 오류율을 가진 몇 시간 버킷을 확인했습니다. Kusto를 사용하여 이 시간 프레임을 확대하고 상위 오류를 찾으려는 '메시지' 열에 집계되는 쿼리를 실행합니다. 메시지의 전체 스택 추적에서 관련 부분을 잘라서 페이지에 더 잘 맞춥니다. 상위 8 개 오류로 좋은 성공을 거두었지만 변경 데이터가 포함 된 형식 문자열에 의해 오류 메시지가 만들어졌기 때문에 오류의 긴 꼬리에 도달했습니다. 

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
|7125|메서드 'RunCycleFromInterimData'에 대한 실행 알고리즘 메서드가 실패했습니다...
|7125|추론호스트 서비스 호출이 실패했습니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않음...
|7124|예기치 않은 추론 시스템 오류입니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않음... 
|5112|예기치 않은 추론 시스템 오류입니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않습니다.
|174|추론호스트 서비스 호출이 실패했습니다.System.ServiceModel.Communication예외: 파이프에 오류가 기록되었습니다....
|10|메서드 'RunCycleFromInterimData'에 대한 실행 알고리즘 메서드가 실패했습니다...
|10|시스템 오류 추론. Microsoft.Bing.Platform.추론.서비스.관리자.사용자 중간 데이터 관리자 예외:...
|3|추론호스트 서비스 호출이 실패했습니다..System.ServiceModel.통신개체결함 예외:...
|1|추론 시스템 오류... 소셜 그래프.BOSS.운영 응답 ... AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD...
|1|추론 시스템 오류... 소셜 그래프.BOSS.운영 응답 ... AIS 트레이시드 : 5F79F7587FF943EC9B641E02E701AFBF...

운영자가 `reduce` 도움을 주려는 곳입니다. 운영자는 `reduce` 코드의 동일한 추적 계측 점에서 발생한 63개의 서로 다른 오류를 식별하고 해당 시간 창에서 추가의미 있는 오류 추적에 집중할 수 있도록 도와주었습니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|개수|패턴
|---|---
|7125|메서드 'RunCycleFromInterimData'에 대한 실행 알고리즘 메서드가 실패했습니다...
|  7125|추론호스트 서비스 호출이 실패했습니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않음...
|  7124|예기치 않은 추론 시스템 오류입니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않음...
|  5112|예기치 않은 추론 시스템 오류입니다. System.NullReferenceException: 개체 참조가 개체의 인스턴스로 설정되지 않습니다.
|  174|추론호스트 서비스 호출이 실패했습니다.System.ServiceModel.Communication예외: 파이프에 오류가 기록되었습니다....
|  63|시스템 오류 추론. 마이크로소프트.Bing.Platform.추론. \*: \* 오브젝트 BOSS에 쓰기. \*: 소셜그래프.BOSS.Reques...
|  10|메서드 'RunCycleFromInterimData'에 대한 실행 알고리즘 메서드가 실패했습니다...
|  10|시스템 오류 추론. Microsoft.Bing.Platform.추론.서비스.관리자.사용자 중간 데이터 관리자 예외:...
|  3|추론호스트 서비스 호출이 실패했습니다. 시스템.서비스 모델. \*: 개체, System.ServiceModel.Channels. \* \* \* , 에 대한 \* + \*   Syst에서 ...

이제 검색된 이상 현상에 기여한 상위 오류에 대해 잘 볼 수 되었으므로 시스템 전체에서 이러한 오류의 영향을 이해하고 싶습니다. '로그' 테이블에는 '구성 요소', '클러스터' 등과 같은 추가 차원 데이터가 포함되어 있습니다. 새로운 '자동 클러스터' 플러그인은 간단한 쿼리로 그 통찰력을 도출하는 데 도움이 될 수 있습니다. 아래 예제에서는 상위 4개 오류 각각이 구성 요소에 만연한 것을 명확하게 확인할 수 있으며 상위 3개 오류는 DB4 클러스터에만 해당되지만 네 번째 오류는 모든 클러스터에서 발생합니다.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|개수 |백분율(%)|구성 요소|클러스터|메시지
|---|---|---|---|---
|7125|26.64|추론호스트서비스|DB4|메서드에 대한 실행 알고리즘 방법 ....
|7125|26.64|알 수 없는 구성 요소|DB4|추론호스트 서비스 호출이 실패했습니다....
|7124|26.64|추론알고리즘 엑시터|DB4|예기치 않은 추론 시스템 오류...
|5112|19.11|추론알고리즘 엑시터|*|예기치 않은 추론 시스템 오류... 

## <a name="mapping-values-from-one-set-to-another"></a>한 집합에서 다른 집합으로 값 매핑

일반적인 사용 사례는 결과를 보다 다양한 방식으로 채택하는 데 도움이 되는 값의 정적 매핑을 사용하는 것입니다.  
예를 들어 다음 테이블을 갖는 것이 좋습니다. DeviceModel은 장치 이름을 참조하는 매우 편리한 형태가 아닌 장치의 모델을 지정합니다.  


|DeviceModel |개수 
|---|---
|아이폰5,1 |32 
|아이폰3,2 |432 
|아이폰7,2 |55 
|아이폰5,2 |66 

  
더 나은 표현은 다음과 같은 것일 수 있습니다.  

|FriendlyName |개수 
|---|---
|iPhone 5 |32 
|아이폰 4 |432 
|iPhone 6 |55 
|아이폰5 |66 

아래 두 가지 접근 방식은 이를 달성할 수 있는 방법을 보여 줍니다.  

### <a name="mapping-using-dynamic-dictionary"></a>동적 사전을 사용한 매핑

아래 접근 방식은 동적 사전 및 동적 접근자로 매핑을 달성할 수 있는 방법을 보여 주며, 이 방법을 보여 주실 수 있습니다.

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
|아이폰 4|432|
|iPhone 6|55|
|아이폰5|66|



### <a name="mapping-using-static-table"></a>정적 테이블을 사용한 매핑

아래 접근 방식은 영구 테이블 및 조인 연산자를 사용하여 매핑을 달성할 수 있는 방법을 보여 주며,
 
매핑 테이블 만들기(한 번만):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

이제 장치의 콘텐츠:

|DeviceModel |FriendlyName 
|---|---
|아이폰5,1 |아이폰 5 
|아이폰3,2 |아이폰 4 
|아이폰7,2 |아이폰 6 
|아이폰5,2 |아이폰5 


테스트 테이블 소스를 만들기 위한 동일한 트릭:

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
|아이폰 5 |32 
|아이폰 4 |432 
|아이폰 6 |55 
|아이폰5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>쿼리 시간 차원 테이블 만들기 및 사용

대부분의 경우 데이터베이스에 저장되지 않은 일부 임시 차원 테이블과 쿼리 결과를 조인하려고 합니다. 다음과 같은 작업을 수행하여 단일 쿼리로 범위가 조정된 테이블의 결과가 있는 식을 정의할 수 있습니다.

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

약간 더 복잡한 예는 다음과 같습니다.

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>ID당 최신(타임스탬프별) 레코드 검색

`id` 열(사용자 ID 또는 노드 ID와 같이 각 행이 연결된 엔터티 식별)과 `timestamp` 열(행에 대한 시간 참조 제공)과 다른 열이 포함된 테이블이 있다고 가정합니다. 목표는 `id` "최신"이 "가장 높은 값을 `timestamp`갖는"로 정의되는 열의 각 값에 대해 최신 2 개의 레코드를 반환하는 쿼리를 작성하는 것입니다.

이 작업은 최상위 [중첩 연산자를](topnestedoperator.md)사용하여 수행할 수 있습니다.
먼저 쿼리를 제공한 다음 설명하겠습니다.

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
1. 이는 `datatable` 데모를 위해 일부 테스트 데이터를 생성하는 방법일 뿐입니다. 물론 현실에서 데이터를 사용할 수 있습니다.
2. 이 줄은 기본적으로 "의 `id`모든 고유 값을 반환합니다.
3. 그런 다음 `timestamp` 열을 최대화하는 상위 2개 레코드, 이전 수준의 열(여기, `id`그냥)과 이 `timestamp`수준(여기)에 지정된 열에 대해 반환합니다.
4. 이 줄은 `bla` 이전 수준에서 반환된 각 레코드에 대한 열 값을 추가합니다. 테이블에 다른 관심 있는 열이 있는 경우 이러한 모든 열에 대해 이 줄을 반복합니다.
5. 마지막으로 [프로젝트 어웨이 연산자에서](projectawayoperator.md) 도입한 "추가" 열을 `top-nested`제거합니다.

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>전체 계산의 백분율로 테이블 확장

숫자 열을 포함하는 테이블 형식식이 있는 경우 해당 열을 합계의 백분율로 값과 함께 사용자에게 표시하는 것이 좋습니다. 예를 들어 값이 다음 테이블인 일부 쿼리가 있다고 가정합니다.

|일부 시리즈|소틀트 (일부)는|
|----------|-------|
|Foo       |    100|
|가로 막대형       |    200|

이 표를 다음과 같이 표시하려고 합니다.

|일부 시리즈|소틀트 (일부)는|Pct |
|----------|-------|----|
|Foo       |    100|33.3|
|가로 막대형       |    200|66.6|

이렇게 하려면 `SomeInt` 열의 합계(합계)를 계산한 다음 이 열의 각 값을 합계로 나눕니다. 임의의 결과에 대해 다음과 같은 이름을 [연산자로](asoperator.md)사용하여 지정할 수 있습니다.

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

## <a name="performing-aggregations-over-a-sliding-window"></a>슬라이딩 창을 통해 집계 수행
다음 예제에서는 슬라이딩 창을 사용하여 열을 요약하는 방법을 보여 주어 있습니다. 예를 들어, 타임스탬프에 의한 과일 가격을 포함하는 아래 표를 예로 들어 보겠습니다. 7일의 슬라이딩 윈도우를 사용하여 하루에 각 과일의 최소, 최대 및 합계 비용을 계산한다고 가정해 보겠습니다. 즉, 결과 집합의 각 레코드는 지난 7일을 집계하고 결과는 분석 기간의 일일 레코드를 포함합니다.  

과일 테이블: 

|타임스탬프|Fruit|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|바나나|3|
|2018-09-25 20:00:00.0000000|Apples|9|
|2018-09-26 03:00:00.0000000|바나나|4|
|2018-09-27 10:00:00.0000000|매|8|
|2018-09-28 07:00:00.0000000|바나나|6|
|2018-09-29 21:00:00.0000000|바나나|8|
|2018-09-30 01:00:00.0000000|매|2|
|2018-10-01 05:00:00.0000000|바나나|0|
|2018-10-02 02:00:00.0000000|바나나|0|
|2018-10-03 13:00:00.0000000|매|4|
|2018-10-04 14:00:00.0000000|Apples|8|
|2018-10-05 05:00:00.0000000|바나나|2|
|2018-10-06 08:00:00.0000000|매|8|
|2018-10-07 12:00:00.0000000|바나나|0|

슬라이딩 창 집계 쿼리(쿼리 결과 아래에 설명이 제공됩니다):. 

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
|2018-10-01 00:00:00.0000000|매|2|8|10|
|2018-10-02 00:00:00.0000000|바나나|0|8|18|
|2018-10-02 00:00:00.0000000|매|2|8|10|
|2018-10-03 00:00:00.0000000|매|2|8|14|
|2018-10-03 00:00:00.0000000|바나나|0|8|14|
|2018-10-04 00:00:00.0000000|바나나|0|8|14|
|2018-10-04 00:00:00.0000000|매|2|4|6|
|2018-10-04 00:00:00.0000000|Apples|8|8|8|
|2018-10-05 00:00:00.0000000|바나나|0|8|10|
|2018-10-05 00:00:00.0000000|매|2|4|6|
|2018-10-05 00:00:00.0000000|Apples|8|8|8|
|2018-10-06 00:00:00.0000000|매|2|8|14|
|2018-10-06 00:00:00.0000000|바나나|0|2|2|
|2018-10-06 00:00:00.0000000|Apples|8|8|8|
|2018-10-07 00:00:00.0000000|바나나|0|2|2|
|2018-10-07 00:00:00.0000000|매|4|8|12|
|2018-10-07 00:00:00.0000000|Apples|8|8|8|

쿼리 세부 정보: 

쿼리는 입력 테이블의 각 레코드를 7일 동안 "늘이게"(중복) 실제 모양을 게시하여 각 레코드가 실제로 7번 표시되도록 합니다. 결과적으로, 매일 집계를 수행 하는 경우 집계 이전 7 일의 모든 레코드를 포함 합니다.

단계별 설명(숫자는 쿼리 인라인 주석의 숫자를 참조)
1. 각 레코드를 1d로 bin(_start 상대). 
2. 레코드당 범위의 끝을 결정합니다 - _bin + 7d, 이 _범위가 (시작, 끝)_ 범위를 벗어난 경우, 이 경우 조정됩니다. 
3. 각 레코드에 대해 현재 레코드의 날부터 7일(타임스탬프)의 배열을 만듭니다. 
4. mv-배열을 확장하여 각 레코드를 서로 1일 간격으로 7개의 레코드로 복제합니다. 
5. 매일 집계 기능을 수행합니다. #4 인해 실제로 _지난_ 7 일을 요약합니다. 
6. 마지막으로 첫 번째 7d의 데이터가 불완전하기 때문에(처음 7일 동안은 7d 조회 기간이 없음) 최종 결과에서 처음 7일을 제외합니다(2018-10-01의 집계에만 참여). 

## <a name="find-preceding-event"></a>이전 이벤트 찾기
다음 예제에서는 2개의 데이터 집합 간에 이전 이벤트를 찾는 방법을 보여 줍니다.  

*목적:* B의 각 레코드에 대해 2개의 데이터 세트 A와 B가 주어지면 A에서 이전 이벤트를 찾습니다(즉, B보다 여전히 "오래된 A"의 arg_max 레코드). 예를 들어, 아래는 다음 샘플 데이터 집합에 대한 예상 출력입니다. 

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

|타임스탬프|Id|이벤트 B|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|아즈 1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|타임스탬프|Id|이벤트 A|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|b|
|2019-01-01 00:00:04.0000000|x|b|
|2019-01-01 00:00:04.0000000|y|b|
|2019-01-01 00:02:00.0000000|z|b|

예상 출력: 

|Id|타임스탬프|이벤트 B|A_Timestamp|이벤트 A|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|b|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|b|2019-01-01 00:00:00.0000000|아즈 1|

이 문제에 대해 제안된 2가지 접근 법이 있습니다. 특정 데이터 집합에서 둘 다 테스트하여 가장 적합한 데이터 집합을 찾아야 합니다(다른 데이터 집합에서 다르게 수행될 수 있음). 

### <a name="suggestion-1"></a>제안 #1 :
이 제안은 ID와 타임스탬프별로 두 데이터 집합을 직렬화한 다음 모든 B 이벤트를 `arg_max` 앞의 모든 A 이벤트와 그룹화하고 그룹의 모든 As 중에서 선택합니다. 

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
이 제안을 하려면 최대 룩백 기간을 정의해야 합니다(A의 레코드를 B와 비교할 수 있도록 허용하는 "이전"의 양)를 정의한 다음 Id및 이 룩백 기간에 2개의 데이터 집합을 조인합니다. 조인은 가능한 모든 후보(B보다 오래된 모든 A 레코드와 회수 기간 내에 있는 레코드)를 생성한 다음 arg_min(TimestampB – TimestampA)으로 B에 가장 가까운 레코드를 필터링합니다. 회수 기간이 작을수록 쿼리가 더 잘 수행될 것으로 예상됩니다. 

아래 예제에서는 조회 기간이 1m로 설정되어 있으므로 Id 'z'가 있는 레코드에는 해당 'A' 이벤트가 없습니다('A'가 2m 더 오래되었기 때문에).

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

|Id|B_Timestamp|A_Timestamp|이벤트 B|이벤트 A|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|b|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|b|Ay1|
|z|2019-01-01 00:02:00.0000000||b||