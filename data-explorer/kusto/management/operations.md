---
title: 운영 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 작업 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 01f30a8d391948d5466ef76b2951d55aa6084e15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520692"
---
# <a name="operations-management"></a>운영 관리

## <a name="show-operations"></a>.show 작업

`.show``operations` 명령은 지난 2주 동안 실행된 실행 및 완료된 모든 관리 작업이 포함된 테이블을 반환합니다(현재 보존 기간 구성).

**구문**

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

## <a name="show-operation-details"></a>.show 작업 세부 정보

작업은 (선택적으로) 결과를 유지시킬 수 있으며, 작업을 사용하여 `.show` `operation` 작업이 완료되면 검색할 수 있습니다.`details` 

**참고:**

* 모든 컨트롤 명령이 결과를 유지하는 것은 아니며 일반적으로 비동기 `async` 실행(키워드 사용)에서만 기본적으로 수행합니다. 설명서에서 특정 명령을 검색하고 명령이 있는지 확인하십시오(예: [데이터 내보내기](data-export/index.md)참조). 
* 명령의 출력 스키마는 명령의 `.show` `operations` 동기 실행에서 반환된 스키마와 동일합니다. `details` 
* 작업이 성공적으로 완료된 후에만 `details` 명령을 호출할 수 있습니다. `.show` `operation` 표시 [작업 명령)을](#show-operations)사용하여 이 명령을 호출하기 전에 작업의 상태를 확인합니다. 

**구문**

`.show``operation` *오퍼레이션 ID*`details`

**결과**

결과는 작업 유형마다 다르며 동기적으로 실행될 때 작업 결과의 스키마와 일치합니다. 

**예**

이 예제의 *OperationId는* [데이터 내보내기](../management/data-export/index.md) 명령 중 하나의 비동기 실행에서 반환된 명령입니다.

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```
비동기 내보내기 명령은 다음 작업 ID를 반환했습니다.

|오퍼레이션 ID|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

이 작업 ID는 명령이 완료되어 내보낸 Blob을 쿼리할 때 다음과 같이 사용할 수 있습니다. 

```
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**결과**

|경로|숫자 레코드|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|