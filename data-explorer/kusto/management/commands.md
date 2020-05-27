---
title: 명령 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 명령 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011417"
---
# <a name="commands-management"></a>명령 관리

## <a name="show-commands"></a>. 명령 표시 

`.show commands`최종 상태에 도달한 관리자 명령이 있는 테이블을 반환 합니다. 이러한 명령은 30 일 동안 쿼리 하는 데 사용할 수 있습니다.

Commands 테이블에는 완료 된 모든 명령에 대 한 리소스 사용량 세부 정보가 포함 된 두 개의 열이 있습니다.

* TotalCpu-이 명령에서 사용 하는 총 CPU 클록 시간 (사용자 모드 + 커널 모드)입니다.
* ResourceUtilization-TotalCpu를 포함 하 여 해당 명령과 관련 된 모든 리소스 사용 정보를 포함 합니다.

추적 되는 리소스 사용에는 데이터 업데이트 및 현재 관리자 명령과 관련 된 모든 쿼리가 포함 됩니다.
현재 일부 관리자 명령만 명령 테이블 ( `.ingest` , `.set` , `.append` , `.set-or-replace` , `.set-or-append` )에 적용 됩니다. 점차 많은 명령이 명령 테이블에 추가 됩니다.

* 데이터베이스 [관리자 또는 데이터베이스 모니터](../management/access-control/role-based-authorization.md) 는 해당 데이터베이스에서 호출 된 모든 명령을 볼 수 있습니다.
* 다른 사용자는 해당 사용자가 호출한 명령만 볼 수 있습니다.

**구문**

`.show` `commands`
 
**예제**
 
|ClientActivityId |CommandType |텍스트 |데이터베이스 |StartedOn |LastUpdatedOn |기간 |시스템 상태 |RootActivityId |사용자 |FailureReason |애플리케이션 |주 서버 |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |. 비동기 작업 병합 ...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |완료됨  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD 앱 id = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM .Svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |{"ScannedExtentsStatistics": {"Min Cannedbtime": null, "MaxCacheStatistics Cannedtime": null}, "": {Memory ": {" 누락 ": 2," 적중 ": 20}," Disk ": {" 누락 ": 2," 적중 ": 0}}," MemoryPeak ": 159620640," TotalCpu ":" 00:00:03.5781250 "} 
|KE. 함수 710e08ca-2cd3-4d2d-b7bd-2738d335aa50    |DataIngestPull |MyTableName으로 수집 ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Failed |a8986e9e-81b0270d6fae4    |cooper@fabrikam.com    |소켓 연결이 삭제 되었습니다.   |Kusto.Explorer |aaduser = ...    |00:00:00   |{"ScannedExtentsStatistics": {"Min Cannedbtime": null, "MaxCacheStatistics Cannedtime": null}, "": {"Memory": {"누락": 0, 적중 ": 0}," Disk ": {" 누락 ": 0," 적중 ": 0}}," MemoryPeak ": 0," TotalCpu ":" 00:00:00 "} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ExtentsRebuild |. 비동기 작업 병합 ...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |완료됨  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD 앱 id = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM .Svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |{"ScannedExtentsStatistics": {"Min Cannedbtime": null, "MaxCacheStatistics Cannedtime": null}, "": {Memory ": {" 누락 ": 0," 적중 ": 1}," Disk ": {" 누락 ": 0," 적중 ": 0}}," MemoryPeak ": 88828560," TotalCpu ":" 00:00:00.8906250 "} 

**예: ResourceUtilization 열에서 특정 데이터 추출**

ResourceUtilization 열 내의 속성 중 하나에 액세스 하는 작업은 ResourcesUtilization.xxx를 호출 하 여 수행 됩니다. 여기서 xxx는 속성 이름입니다.
> [!NOTE] 
> `ResourceUtilization`는 동적 열입니다. 해당 값을 사용 하려면 먼저 특정 데이터 형식으로 변환 해야 합니다. ,,과 같은 변환 함수를 사용 `tolong` `toint` `totimespan` 합니다.  

예제:

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak |TotalCpu |텍스트
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500 |. append Server_Boots <\| Let bootStartsSourceTable = SessionStarts; ...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750 |. set 또는-추가 WebUsage <\| database (' CuratedDB '). WebUsage_v2 | 요약 ... | 프로젝트 ...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000 |. set-또는-append Asclustereventstats (...) <\| Atlas_Temp (datetime (2017-09-28 12:13:28.7621737), datetime (2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>성능 문제 조사

`.show``commands`는 각 제어 명령에서 사용 하는 리소스를 표시 하므로 성능 문제를 조사 하는 데 사용할 수 있습니다.

다음 예는 이러한 조사에 대 한 단순 하 고 유용한 쿼리입니다.

### <a name="query-the-totalcpu-column"></a>TotalCpu 열 쿼리

마지막 날의 상위 10 개 CPU 소비 쿼리입니다.

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

TotalCpu가 3 분 동안 경과한 지난 10 시간의 모든 쿼리입니다.

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

TotalCpu가 5 분 동안 경과한 최근 24 시간 동안의 모든 쿼리는 사용자 및 주체로 그룹화 됩니다.

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

시간 차트: 평균 CPU 및 95 번째 백분위 수 및 최대 CPU

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>MemoryPeak 쿼리

가장 높은 MemoryPeak 값을 사용 하 여 마지막 날의 상위 10 개 쿼리

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

시간 차트 Average MemoryPeak vs 95 백분위 수 vs Max MemoryPeak.

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
