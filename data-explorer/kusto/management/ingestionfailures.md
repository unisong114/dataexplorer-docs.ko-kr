---
title: 수집 실패 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인기 오류에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 699fd01e9a284179bf2749b58581392d2170f0ca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520947"
---
# <a name="ingestion-failures"></a>인고 실패

## <a name="show-ingestion-failures"></a>.show 인비전에실패

이 명령은 데이터 수집 제어 명령을 실행하는 동안 발생한 모든 수집 실패를 포함하는 결과 [집합을 반환합니다.](data-ingestion/index.md)

*참고:* 
- 수집 흐름의 다른 부분(예: 데이터 수집 제어 명령이 Kusto의 데이터 엔진 서비스로 전송되기 전)에서 발생하는 수집 실패는 이 명령의 결과 집합에 나타나지 않습니다.
- [큐에 대기된 섭취와](../api/netfx/about-kusto-ingest.md#queued-ingestion)관련된 흐름에서 발생하는 모니터링 실패의 경우 [이 가이드를](../api/netfx/kusto-ingest-client-status.md)따르는 것이 좋습니다.

**구문**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |기록된 모든 기록된 기록 된 변환 실패를 반환 합니다.  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |필터링된 인비전에실패 세트 반환
|`.show`오퍼레이션 *ID* `ingestion` `failures` `with(OperationId="``")` |특정 작업 ID에 대한 인베이션 실패 반환

**결과**
 
|출력 매개 변수 |Type |Description 
|---|---|---
|오퍼레이션 ID |String |작업 식별자. 이 매개 변수는 [.show 작업](operations.md) 명령을 실행 하여 추가 작업 세부 정보를 볼 수 있습니다. 
|데이터베이스 |String |오류가 발생한 데이터베이스
|테이블 |String |오류가 발생한 테이블
|실패 |DateTime |실패가 등록된 날짜/시간(UTC) 
|인제션소스패스 |String |인시션 소스 식별(일반적으로 Azure Blob URI) 
|세부 정보 |String |실패 세부 정보. 실제 섭취 실패 근본 원인에 대한 통찰력 제공
|실패Kind |String |오류 유형(영구/과도)
|RootActivityId |String |루트 활동 ID.
|오퍼레이션Kind |String |오류가 등록된 동안의 섭취 작업 유형(단계)
|시작에서시작업데이트정책 |부울 | [업데이트 정책을](update-policy.md) 실행하는 동안 오류가 등록되었는지 여부를 나타냅니다.
 
**예제**
 
|오퍼레이션 ID |데이터베이스 |테이블 |실패 |인제션소스패스 |세부 정보 |실패Kind |RootActivityId |오퍼레이션Kind |시작에서시작업데이트정책
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395 |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... Url... |ID '*****.csv'가 있는 스트림에는 잘못된 Csv 형식이 있습니다. |영구 |3c83942-e446-4999-9b00-d4c664f06ef6 |데이터 수집풀 | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... Url... |ID '*****.csv'가 있는 스트림에는 잘못된 Csv 형식이 있습니다. |영구 |48571bdb-b714-4f32-8ddc-4001838a956c |데이터 수집풀 | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... Url... |ID '*****.csv'가 있는 스트림에는 잘못된 Csv 형식이 있습니다. |영구 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |데이터 수집풀 | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... Url... |알 수 없는 오류가 발생했습니다: 'System.Exception' 형식의 예외가 throw되었습니다. |임시 |9b7bb017-471e-48f6-9c96-d16fcf938d2a |데이터 수집풀 | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... Url... |Blob을 다운로드하지 못했습니다: 클라이언트가 지정된 시간 시간 내에 작업을 완료할 수 없습니다. |영구 |21fa0d6-cd7d-4493-b6f7-78916ce0d617 |데이터 수집풀 | 0