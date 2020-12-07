---
title: 진단 정보-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 진단 정보에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d106d150ca7041c49b4d8eeaa703681c5f461fa8
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321542"
---
# <a name="diagnostic-information"></a>진단 정보

이러한 명령을 사용 하 여 시스템 진단 정보를 표시할 수 있습니다.

* [`.show cluster`](#show-cluster)
* [`.show diagnostics`](#show-diagnostics)
* [`.show capacity`](#show-capacity)
* [`.show operations`](#show-operations)

## <a name="show-cluster"></a>. 클러스터 표시

```kusto
.show cluster
```

클러스터에서 현재 활성 상태인 각 노드에 대해 하나의 레코드를 포함 하는 집합을 반환 합니다.  

**결과** 

|출력 열 |형식 |설명
|---|---|---|
|NodeId|String|노드를 식별 합니다. 클러스터를 Azure에 배포 하는 경우 노드 ID는 노드의 Azure RoleId
|주소|String |노드 간 통신을 위해 클러스터에서 사용 하는 내부 끝점입니다.
|Name |String |노드의 내부 이름입니다. 이름에는 컴퓨터 이름, 프로세스 이름 및 프로세스 ID가 포함 됩니다.
|StartTime |DateTime |노드에서 현재 Kusto 인스턴스화가 시작 된 날짜/시간 (UTC)입니다. 이 값은 노드 (또는 노드에서 실행 중인 Kusto)가 최근에 다시 시작 되었는지 여부를 검색 하는 데 사용할 수 있습니다.
|IsAdmin |부울 |이 노드가 현재 클러스터의 "리더" 인 경우 
|MachineTotalMemory  |Int64 |노드의 RAM 크기입니다.
|MachineAvailableMemory  |Int64 |현재 노드에서 사용할 수 있는 RAM 양입니다.
|ProcessorCount  |Int32 |노드의 프로세서 수입니다.
|환경 설명  |문자열 |JSON으로 직렬화 된 노드의 환경에 대 한 추가 정보입니다. 예: 업그레이드/장애 도메인

**예제**

```kusto
.show cluster
```

NodeID|주소|Name|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|환경 설명
---|---|---|---|---|---|---|---|---
Kusto.Azure.Svc_IN_1|net.tcp://100.112.150.30:23107/|Kusto.Azure.Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02:00:22.6522152|참|274877435904|247797796864|16|{"UpdateDomain": 0, "FaultDomain": 0}
Kusto.Azure.Svc_IN_3|net.tcp://100.112.154.34:23107/|Kusto.Azure.Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|{"UpdateDomain": 1, "FaultDomain": 1}
Kusto.Azure.Svc_IN_2|net.tcp://100.112.128.40:23107/|Kusto.Azure.Svc_IN_2/RD000D3AB1E054/WaWorkerHost/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|{"UpdateDomain": 2, "FaultDomain": 2}
Kusto.Azure.Svc_IN_0|net.tcp://100.112.138.15:23107/|Kusto.Azure.Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|{"UpdateDomain": 3, "FaultDomain": 3}


## <a name="show-diagnostics"></a>. 진단 표시

```kusto
.show diagnostics
```

Kusto 클러스터 상태에 대 한 정보를 반환 합니다.
 
**반환**

|출력 매개 변수 |형식 |설명|
|-----------------|-----|-----------| 
|I시트|부울|클러스터가 정상 상태 이면이 고, 그렇지 않으면입니다.
|IsScaleOutRequired|부울|더 많은 컴퓨팅 노드를 추가 하 여 클러스터 크기를 늘려야 하는 경우
|MachinesTotal|Int64|클러스터의 컴퓨터 수
|MachinesOffline|Int64|현재 오프 라인 상태인 컴퓨터의 수
|NodeLastRestartedOn|DateTime|클러스터의 노드를 마지막으로 다시 시작한 날짜/시간
|AdminLastElectedOn|DateTime|클러스터 관리자 역할의 마지막 날짜/시간 소유권이 변경 됨
|MemoryLoadFactor|Double|100.0의 최대 용량을 기준으로 클러스터가 보유 하 고 있는 데이터의 양
|ExtentsTotal|Int64|모든 데이터베이스 및 모든 테이블에서 클러스터가 현재 보유 하 고 있는 총 데이터 익스텐트 수입니다.
|예약됨|Int64|
|예약됨|Int64|
|InstancesTargetBasedOnDataCapacity|Int64|ClusterDataCapacityFactor를 80 미만으로 가져오는 데 필요한 인스턴스 수입니다. 이 값은 모든 컴퓨터의 크기가 동일한 경우에만 유효 합니다.
|TotalOriginalDataSize|Int64|원래 수집 데이터의 총 크기 (바이트)입니다.
|TotalExtentSize|Int64|압축 및 인덱싱 후 저장 된 데이터의 총 크기 (바이트)
|IngestionsLoadFactor|Double|사용 된 클러스터 수집 용량의 백분율입니다. 명령을 사용 하 여 최대 용량을 볼 수 있습니다. `.show capacity`
|IngestionsInProgress|Int64|현재 수행 중인 수집 작업의 수입니다.
|IngestionsSuccessRate|Double|이전 10 분 동안 성공적으로 완료 된 수집 작업의 백분율입니다.
|MergesInProgress|Int64|현재 수행 중인 익스텐트 병합 작업의 수입니다.
|BuildVersion|String|클러스터에 배포 된 Kusto 소프트웨어 버전
|BuildTime|DateTime|Kusto 소프트웨어 빌드 버전의 날짜/시간입니다.
|ClusterDataCapacityFactor|Double|사용 된 클러스터 데이터 용량의 백분율입니다. 백분율은 합계 (익스텐트 크기 데이터)/합계 (SSD 캐시 크기)로 계산 됩니다.
|IsDataWarmingRequired|부울|내부: 클러스터의 준비 쿼리를 실행 하 여 로컬 SSD 캐시로 데이터 가져오기 
|DataWarmingLastRunOn|DateTime|웜 데이터가 클러스터에서 실행 된 마지막 날짜/시간입니다.
|MergesSuccessRate|Double|이전 10 분 동안 성공적으로 완료 된 병합 작업의 백분율입니다.
|NotHealthyReason|String|클러스터가 정상이 아닌 이유를 지정 합니다. 
|IsAttentionRequired|부울|클러스터에 작업 팀 주의가 필요한 경우
|Attyonereason|String|클러스터가 주의가 필요한 이유를 지정 합니다.
|ProductVersion|String|제품 정보 (분기, 버전 등)를 지정 합니다.
|FailedIngestOperations|Int64|이전 10 분 동안 실패 한 수집 작업 수
|FailedMergeOperations|Int64|이전 1 시간 동안 실패 한 병합 작업 수
|MaxExtentsInSingleTable|Int64|테이블의 최대 익스텐트 수 (TableWithMaxExtents)
|TableWithMaxExtents|String|최대 익스텐트 수 (MaxExtentsInSingleTable)가 있는 테이블
|WarmExtentSize|Double|핫 캐시의 총 익스텐트 크기 (바이트)입니다.
|NumberOfDatabases|Int32|클러스터의 데이터베이스 수

## <a name="show-capacity"></a>. 용량 표시

```kusto
.show capacity
```

각 리소스에 대 한 예상 클러스터 용량의 계산 결과를 반환 합니다.
 
**결과**

|출력 매개 변수 |형식 |설명 
|---|---|---
|리소스 |String |리소스의 이름입니다. 
|합계 |Int64 |사용할 수 있는 ' Resource ' 형식의 총 리소스 양입니다. 예를 들어 동시 ingestions 수
|사용 |Int64 |현재 사용 된 ' 리소스 ' 유형의 리소스 양
|남은 수 |Int64 |' Resource ' 형식의 남은 리소스 양
 
**예제**

|리소스 |합계 |사용 |남은 수
|---|---|---|---
|ingestions |576 |1 |575

## <a name="show-operations"></a>. 작업 표시

이 명령은 새 관리 노드가 선택 된 이후의 모든 관리 작업을 포함 하는 테이블을 반환 합니다.

|구문 옵션 |설명|
|---|---| 
|`.show` `operations`              |클러스터가 처리 중이거나 처리 한 모든 작업을 반환 합니다.
|`.show``operations` *OperationId*|특정 ID에 대 한 작업 상태를 반환 합니다.
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` )|특정 Id에 대 한 작업 상태를 반환 합니다.

**결과**
 
|출력 매개 변수 |형식 |Description
|---|---|---
|ID |String |작업 식별자
|연산 |String |관리자 명령 별칭
|NodeId |String |명령에서 DataIngestPull와 같은 동작을 원격으로 실행 하는 경우입니다. 노드 ID는를 실행 하는 원격 노드의 ID를 포함 합니다.
|StartedOn |DateTime |작업이 시작 된 날짜/시간 (UTC) 
|LastUpdatedOn |DateTime |작업이 마지막으로 업데이트 된 날짜/시간 (UTC)입니다. 작업은 작업 내의 단계 이거나 완료 단계 일 수 있습니다.
|기간 |DateTime |LastUpdateOn와 StartedOn 사이의 시간 범위
|시스템 상태 |String |"InProgress", "Completed" 또는 "Failed" 값을 포함 하는 명령 상태
|Status |String |실패 한 작업에 대 한 오류를 포함 하는 추가 도움말 문자열
 
**예제**
 
|ID |연산 |노드 ID |시작 시간 |마지막 업데이트 날짜 |기간 |시스템 상태 |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |완료됨 | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto.Azure.Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |완료됨 | 
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |완료됨 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Failed |컬렉션이 수정 되었습니다. 열거 작업이 실행 되지 않을 수 있습니다. |
