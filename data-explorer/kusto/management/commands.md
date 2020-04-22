---
title: 명령 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 명령 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744319"
---
# <a name="commands-management"></a>명령 관리

## <a name="show-commands"></a>.show 명령 

`.show``commands` 명령은 최종 상태에 도달한 관리자 명령이 있는 테이블을 반환합니다. 이러한 명령은 30일 동안 쿼리할 수 있습니다.

Commands 테이블에는 완료된 모든 명령의 리소스 소비 세부 정보가 있는 두 개의 열이 있습니다.
* TotalCpu: 이 명령에서 사용하는 총 CPU 클럭 시간(사용자 모드 + 커널 모드)입니다.
* 리소스 사용률: 해당 명령과 관련된 모든 리소스 사용률 정보(TotalCpu 포함)를 포함하는 개체입니다.

추적중인 리소스 소비에는 현재 관리자 명령과 연결된 모든 쿼리뿐만 아니라 데이터 업데이트가 포함됩니다.
현재 일부 관리자 명령만 명령 테이블(.ingest, .set, .append, .set-or-replace, .set-or-append)에 의해 다루어지며, 점차적으로 더 많은 명령이 추가될 것입니다.


* [데이터베이스 관리자 또는 데이터베이스 모니터는](../management/access-control/role-based-authorization.md) 데이터베이스에서 호출된 모든 명령을 볼 수 있습니다.
* 다른 사용자는 호출된 명령만 볼 수 있습니다.

**구문**

`.show` `commands`
 
**예제**
 
|클라이언트 활동 ID |CommandType |텍스트 |데이터베이스 |시작 일 |마지막 업데이트 켜기 |Duration |시스템 상태 |RootActivityId |사용자 |실패 이유 |애플리케이션 |주 서버 |총 Cpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |익스텐트병합   |.merge 비동기 작업 ...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Completed  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD 앱 ID =5ba8cec2-9a70-e92c98CAD651  |   |쿠스토.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98CAD651  |00:00:03.5781250   |{ "스캔익통계": { "최소데이터 스캔시간": null, "MaxDataScannedTime": null }, "캐시 통계": {메모리": {"Misses": 2, "조회수": 20}, "디스크": {"Misses": {"Misses": 2, "조회수": 0}, "메모리피크": 159620640, "TotalCpu": "TotalCpu": "00:00:03.5715" } 
|애. RunCommand;710e08ca-2cd3-4d2d-b7bd-2738d35aa50 |데이터 수집풀 |.ingest 로 MyTableName ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |실패 |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |소켓 연결이 삭제되었습니다.   |Kusto.Explorer |aaduser=...    |00:00:00   |{ "스캔익통계": {"최소DataScannedTime": null, "MaxDataScannedTime": null} , "캐시통계": {"메모리": {"Misses": 0}, "디스크": {"Misses": 0, "조회수", 0} "메모리피크": 0 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |익스텐트리빌드 |.merge 비동기 작업 ...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Completed  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD 앱 ID =5ba8cec2-9a70-e92c98CAD651  |   |쿠스토.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98CAD651  |00:00:00.8906250   |{ "스캔익통계": { "최소데이터 스캔시간": null, "MaxDataScannedTime": null }, "캐시 통계": {"Misses": {"Misses": 1}, "디스크": {"Misses": {"Misses": 0} "조회수": 0}, "메모리피크": 88828560, "TotalCpu": "00:00:00.890"} 

**예: ResourceUtilization 열에서 특정 데이터 추출**

ResourceUtilization 열 내의 속성 중 하나에 액세스하는 작업은 ResourcesUtilization.xxx 호출하여 수행됩니다(여기서 xxx는 속성 이름임).
ResourceUtilization는 동적 열이므로 해당 값으로 작업하려면 먼저 변환 함수를 사용하여 특정 데이터 유형으로 변환해야 합니다.: tolong, toint, totimespan, ...).  

다음은 그 예입니다.

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|시작 일 |메모리 피크 |총 Cpu |텍스트
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .<부트스타트소스테이블 = 세션스타트를 할 수 있도록 \| Server_Boots 부속; ...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .set-or-가 웹 사용 \| <데이터베이스('큐레이트DB')를 추가했다. WebUsage_v2 | 요약... | 프로젝트...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | Atlas_Temp <(2017-09-28 \| 12:28:28:28.7621737), 날짜 시간(2017-09-28 12:14:14:28.8168492))

## <a name="investigating-performance-issues"></a>성능 문제 조사

`.show``commands` 각 제어 명령에서 사용하는 리소스를 볼 수 있도록 성능 문제를 조사하는 데 사용할 수 있습니다.
다음 예제는 이러한 조사에 대 한 간단 하 고 유용한 쿼리입니다.

### <a name="querying-the-totalcpu-column"></a>TotalCpu 열 쿼리

마지막 날에 상위 10개의 CPU 소모 쿼리:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

TotalCpu가 3분을 초과한 지난 10시간 동안의 모든 쿼리:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

TotalCpu가 5분 이상인 지난 24시간 동안의 모든 쿼리는 사용자 및 보안 주체별로 그룹화됩니다.

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

시간차트: 평균 CPU 대 95 번째 백분위수 대 최대 CPU :

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>메모리 피크 쿼리

메모리피크 값이 가장 높은 마지막 날의 상위 10개 쿼리:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

평균 메모리 피크의 시간 차트 대 95 백분위수 대 최대 메모리 피크:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
