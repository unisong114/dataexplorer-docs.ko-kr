---
title: Operations management-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 작업 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9c25900817aadcc450696525be8446d385d2e220
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610477"
---
# <a name="operations-management"></a>운영 관리

## <a name="show-operations"></a>. 작업 표시 

`.show``operations`명령을 실행 중이 고 완료 된 모든 관리 작업이 포함 된 테이블을 반환 합니다 .이는 마지막 2 주 (현재는 보존 기간 구성)에서 실행 되었습니다.

**구문**

|구문 옵션|설명|
|---|---| 
|`.show` `operations`              |클러스터가 처리 중이거나 클러스터가 처리 한 작업을 모두 반환 합니다.
|`.show``operations` *OperationId*|특정 ID에 대 한 작업 상태를 반환 합니다. 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` )|특정 Id에 대 한 작업 상태를 반환 합니다.

**결과**
 
|출력 매개 변수 |Type |Description
|---|---|---
|ID |String |작업 식별자
|작업(Operation) |String |관리자 명령 별칭
|NodeId |String |명령에 원격 실행이 있는 경우 (예: DataIngestPull)-NodeId는 실행 중인 원격 노드의 ID를 포함 합니다.
|StartedOn |DateTime |작업이 시작 된 날짜/시간 (UTC)
|LastUpdatedOn |DateTime |작업이 마지막으로 업데이트 된 날짜/시간 (UTC)입니다 (작업 내의 단계 이거나 완료 단계 일 수 있음).
|Duration |DateTime |LastUpdateOn와 StartedOn 사이의 TimeSpan
|시스템 상태 |String |명령 상태-"InProgress", "Completed" 또는 "Failed" 값을 가질 수 있습니다.
|상태 |String |실패 한 작업의 오류를 포함 하는 추가 도움말 문자열
 
**예제**
 
|ID |작업(Operation) |노드 ID |시작 시간 |마지막 업데이트 날짜 |Duration |시스템 상태 |상태 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |완료됨 |
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto. Azure Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |완료됨 |
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |완료됨 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |실패 |컬렉션이 수정 되었습니다. 열거 작업이 실행 되지 않을 수 있습니다.

## <a name="show-operation-details"></a>. 작업 세부 정보 표시

작업은 결과를 유지할 수 있으며,를 사용 하 여 작업이 완료 되 면 결과를 검색할 수 있습니다 `.show` `operation` `details` .

> [!NOTE]
> 모든 제어 명령이 결과를 유지 하는 것은 아닙니다. 이러한 명령은 일반적으로 키워드를 사용 하 여 비동기 실행 에서만 기본적으로 수행 `async` 합니다. 특정 명령에 대 한 설명서를 참조 하 고 있는지 확인 합니다. 예를 들어 [데이터 내보내기](data-export/index.md)를 참조 하세요.
> 명령의 출력 스키마는 `.show` `operations` `details` 명령의 동기 실행에서 반환 된 것과 동일한 스키마입니다.
> `.show` `operation` `details` 작업을 성공적으로 완료 한 후에만 명령을 호출할 수 있습니다. 작업 [표시 명령을](#show-operations)사용 하 여이 명령을 실행 하기 전에 작업 상태를 확인할 수 있습니다.

**구문**

`.show``operation` *OperationId*`details`

**결과**

결과는 작업 유형별로 다르며, 동기적으로 실행 되는 경우 작업 결과의 스키마와 일치 합니다.

**예**

예제의 *OperationId* 는 [데이터 내보내기](../management/data-export/index.md) 명령 중 하나의 비동기 실행에서 반환 됩니다.

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 
```

비동기 내보내기 명령이 다음 작업 ID를 반환 했습니다.

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

이 작업 ID는 내보낸 blob을 쿼리 하기 위해 명령이 완료 된 경우에 사용할 수 있습니다. 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

|경로|NumRecords |
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
