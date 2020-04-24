---
title: 진단 정보 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 진단 정보에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ae8efdf99b7ed91285e90defed7568d2a440240d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521270"
---
# <a name="diagnostic-information"></a>진단 정보

다음 명령을 사용하여 시스템 진단 정보를 표시할 수 있습니다.

## <a name="show-cluster"></a>.show 클러스터

```kusto
.show cluster
```

클러스터에서 현재 활성 상태인 노드당 하나의 레코드가 있는 집합을 반환합니다.  

**결과**

|출력 열 |Type |Description 
|---|---|---|
|NodeId|String|노드를 식별합니다(클러스터가 Azure에 배포된 경우 노드의 Azure RoleId입니다). |
|주소|String |노드 간 통신에 클러스터에서 사용하는 내부 끝점입니다. 
|이름 |String |노드의 내부 이름(여기에는 컴퓨터 이름, 프로세스 이름 및 프로세스 ID가 포함됩니다). 
|StartTime |DateTime |노드의 현재 Kusto 인스턴스화가 시작된 정확한 날짜/시간(UTC)입니다. 이 노드 (또는 노드에서 실행 중인 Kusto)가 최근에 다시 시작되었는지 감지하는 데 사용할 수 있습니다. 
|IsAdmin |부울 |이 노드가 현재 클러스터의 "리더"인지 여부입니다. 
|기계총계메모리  |Int64 |노드에 있는 RAM의 양입니다. 
|기계 사용 가능 메모리  |Int64 |노드에서 현재 "사용할 수 있는" RAM의 양입니다. 
|ProcessorCount  |Int32 |노드의 프로세서 양입니다. 
|환경 설명  |문자열 |노드 환경(예: 업그레이드/장애 도메인)에 대한 추가 정보(예: JSON로 직렬화). 

**예제**

```kusto
.show cluster
```

NodeId|주소|이름|StartTime|IsAdmin|기계총계메모리|기계 사용 가능 메모리|ProcessorCount|환경 설명
---|---|---|---|---|---|---|---|---
쿠스토.Azure.Svc_IN_1|net.tcp://100.112.150.30:23107/|Kusto.Azure.Svc_IN_4/RD000D3AB1E9BD/와워커호스트/3820|2016-01-15 02:00:22.6522152|True|274877435904|247797796864|16|{"업데이트 도메인":0, "장애 도메인":0}
쿠스토.Azure.Svc_IN_3|net.tcp://100.112.154.34:23107/|Kusto.Azure.Svc_IN_3/RD000D3AB1E062/와워커호스트/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|{"업데이트 도메인":1, "FaultDomain":1}
쿠스토.Azure.Svc_IN_2|net.tcp://100.112.128.40:23107/|Kusto.Azure.Svc_IN_2/RD000D3AB1E054/와워커호스트/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|{"업데이트 도메인":2, "FaultDomain":2}
쿠스토.Azure.Svc_IN_0|net.tcp://100.112.138.15:23107/|Kusto.Azure.Svc_IN_0/RD000D3AB0D6C6/와워커호스트/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|{"업데이트 도메인":3, "장애 도메인":3}


## <a name="show-diagnostics"></a>.show 진단

```kusto
.show diagnostics
```

Kusto 클러스터 상태 상태에 대한 정보를 반환합니다.
 
**반환**

|출력 매개 변수 |Type |Description|
|-----------------|-----|-----------| 
|IsHealthy|부울|클러스터가 정상으로 간주되는지 여부입니다.
|IsScaleOut필수|부울|클러스터의 크기를 늘려야 하는지 여부(컴퓨팅 노드 추가). 
|기계 총|Int64|클러스터의 컴퓨터 수입니다.
|기계 오프라인|Int64|현재 오프라인 상태입니다(응답하지 않음)의 수입니다.
|노드라스트리스타트온|DateTime|클러스터의 노드가 마지막으로 다시 시작된 시간입니다.
|관리자마지막선출|DateTime|클러스터 관리자 역할의 마지막으로 소유권이 변경되었습니다.
|메모리로드 팩터|Double|용량대비 클러스터가 보유한 데이터 양(100.0)입니다.
|익스텐트합계|Int64|클러스터가 현재 모든 데이터베이스와 모든 테이블에 대해 가지고 있는 총 데이터 범위 수입니다.
|Reserved|Int64|
|Reserved|Int64|
|인스턴스대상기반데이터용량|Int64| ClusterDataCapacityFactor를 80 미만으로 가져오는 데 필요한 인스턴스 수입니다(모든 컴퓨터의 크기가 같은 경우에만 유효함).
|토탈 오리지널 데이터크기|Int64|원래 수집된 데이터의 총 크기
|총익 크기|Int64|저장된 데이터의 총 크기(압축 및 인덱싱 후)
|IngestionsLoadFactor|Double|클러스터 사용 용량의 사용률(.show 용량 명령을 사용하여 표시)
|섭취진행중|Int64|현재 수행 중인 작업 수입니다.
|섭취성공률|Double|지난 10분 동안 성공적으로 완료된 작업 의 비율입니다.
|병합진행상황|Int64|현재 수행 중인 작업을 병합하는 익스텐트 수입니다.
|BuildVersion|String|클러스터에 배포된 Kusto 소프트웨어 버전입니다.
|빌드 타임|DateTime|이 Kusto 소프트웨어 버전의 빌드 시간입니다.
|ClusterDataCapacityFactor|Double|클러스터 데이터 용량 사용률의 백분율입니다. SUM(익스텐트 크기 데이터) / SUM(SSD 캐시 크기)으로 계산됩니다.
|IsData웜데필요한|부울|내부: 로컬 SSD 캐시로 데이터를 가져오기 위해 클러스터의 워밍업 쿼리를 실행할지 여부입니다. 
|데이터 워밍라스트런온|DateTime|클러스터에서 마지막으로 .warm 데이터가 실행되었습니다.
|병합성공률|Double|지난 10분 동안 성공적으로 완료된 병합 작업의 백분율입니다.
|NotHealthyReason|String|클러스터가 정상이 아닌 이유를 지정하는 문자열 
|IsAttention필수|부울|클러스터에 운영 팀의 주의가 필요한지 여부
|주의필수이유|String|주의가 필요한 클러스터의 이유를 지정하는 문자열
|ProductVersion|String|제품 정보가 있는 문자열(분기, 버전 등)
|실패한 작업|Int64|10분 간 실패한 수의 수
|실패한 병합작업|Int64|실패한 병합 작업 수 1시간
|맥스익스익스인싱글테이블|Int64|테이블의 최대 익스텐트 수(TableWithMaxExtents)
|테이블위드맥스익스|String|최대 익스텐트 수가 있는 테이블(MaxExtentsInSingleTable)
|웜익스텐트크기|Double|핫 캐시의 총 익스텐트 크기
|숫자OfDatabases|Int32|클러스터의 데이터베이스 수

## <a name="show-capacity"></a>.show 용량 

```kusto
.show capacity
```

각 리소스에 대한 예상 클러스터 용량에 대한 계산을 반환합니다. 
 
**결과**

|출력 매개 변수 |Type |Description 
|---|---|---
|리소스 |String |리소스의 이름입니다. 
|합계 |Int64 |사용 가능한 '리소스' 유형의 총 리소스 수(예: 동시 인베이션 양) 
|사용 |Int64 |현재 '리소스' 유형의 리소스 수 
|남은 수 |Int64 |'리소스' 형식의 남은 리소스 수 
 
**예제**

|리소스 |합계 |사용 |남은 수 
|---|---|---|---
|섭취 |576 |1 |575 

## <a name="show-operations"></a>.show 작업 

새 Admin 노드가 선출된 이후 모든 관리 작업이 있는 테이블을 반환합니다. 

|||
|---|---| 
|`.show` `operations`              |클러스터가 처리했거나 처리 중이면 모든 작업을 반환합니다. 
|`.show``operations` *오퍼레이션 ID*|특정 ID에 대한 작업 상태를 반환합니다. 
|`.show``operations` `,` *OperationId2* `,` *OperationId1* OperationId1 OperationId2 ...) `(`|특정 아이디에 대한 작업 상태 반환

**결과**
 
|출력 매개 변수 |Type |Description 
|---|---|---
|Id |String |작업 식별자. 
|작업(Operation) |String |관리자 명령 별칭 
|NodeId |String |명령에 원격 실행(예: DataIngestPull)이 있는 경우 - NodeId에는 실행 원격 노드의 ID가 포함됩니다. 
|시작 일 |DateTime |작업이 시작된 날짜/시간(UTC) 
|마지막 업데이트 켜기 |DateTime |작업이 마지막으로 업데이트된 날짜/시간(UTC) (작업 내부의 단계 또는 완료 단계일 수 있음) 
|Duration |DateTime |마지막 업데이트온과 시작 사이의 시간 범위 
|시스템 상태 |String |명령 상태: "진행 중", "완료됨" 또는 "실패" 값을 가질 수 있습니다. 
|Status |String |실패한 작업에 대한 오류를 보유하는 추가 도움말 문자열 
 
**예제**
 
|Id |작업(Operation) |노드 ID |시작 시작 |마지막 업데이트 날짜 |Duration |시스템 상태 |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395 |스키마쇼 | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |데이터 수집풀 |쿠스토.Azure.Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Completed | 
|e198c519-5263-4629-a158-8d68f7a1022f |운영쇼 | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |익스텐트 드롭 | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |데이터 수집풀 | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |실패 |컬렉션이 수정되었습니다. 열거 작업이 실행되지 않을 수 있습니다. 