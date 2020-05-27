---
title: 수집 오류-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 수집 오류에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 7684ea11b03113051580e3e19aef0d9ac3f13585
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011485"
---
# <a name="ingestion-failures"></a>수집 오류

## <a name="show-ingestion-failures"></a>. 수집 오류 표시


이 명령은 [데이터 수집 제어 명령이](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) 실행 될 때 발생 하는 수집 실패를 포함 하는 결과 집합을 반환 합니다.


> [!NOTE]
> 수집 흐름의 다른 부분에서 발생 하는 수집 오류는이 명령의 결과 집합에 표시 되지 않습니다. 예를 들어 데이터 수집 제어 명령이 Kusto 데이터 엔진 서비스로 전송 되기 전에 이러한 오류가 발생할 수 있습니다.

> [대기 중인](../api/netfx/about-kusto-ingest.md#queued-ingestion)수집을 포함 하는 흐름에서 발생 하는 오류를 모니터링 하는 방법에 대 한 자세한 내용은 [이 가이드](../api/netfx/kusto-ingest-client-status.md)를 참조 하세요.

**구문**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |기록 된 수집 오류를 모두 반환 합니다.  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |수집 실패의 필터링 된 집합을 반환 합니다.
|`.show``ingestion` `failures` `with(OperationId="` *OperationId*`")` |특정 작업 ID에 대 한 수집 실패를 반환 합니다.

**결과**
 
|출력 매개 변수           |유형     |Description                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |를 통해 추가 작업 세부 정보를 보는 데 사용할 수 있는 작업 식별자입니다. <br> [. 작업 명령 표시](operations.md) </br> 
|데이터베이스                   |String   |오류가 발생 한 데이터베이스입니다.
|테이블                      |String   |오류가 발생 한 테이블입니다.
|FailedOn                   |DateTime |오류가 등록 된 날짜/시간 (UTC) 
|IngestionSourcePath        |String   |수집 소스 (일반적으로 Azure Blob URI)를 식별 합니다. 
|세부 정보                    |String   |오류 세부 정보입니다. 실제 수집 실패 근본 원인에 대 한 통찰력을 제공 합니다.
|FailureKind                |String   |실패 유형 (영구/임시)
|RootActivityId             |String   |루트 활동 ID입니다.
|OperationKind              |String   |오류가 등록 된 수집 작업 유형 (단계)입니다.
|OriginatesFromUpdatePolicy |Boolean | [업데이트 정책을](update-policy.md) 실행 하는 동안 오류가 등록 되었는지 여부를 나타냅니다.
 
**예제**
 
|OperationId |데이터베이스 |테이블 |FailedOn |IngestionSourcePath |세부 정보 |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |... url ... |ID가 ' * * * * * .csv ' 인 스트림에 잘못 된 형식의 CSV 형식이 있습니다. * |영구 |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |... url ... |ID가 ' * * * * * .csv ' 인 스트림에 잘못 된 형식의 CSV 형식이 있습니다. * |영구 |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |... url ... |ID가 ' * * * * * .csv ' 인 스트림에 잘못 된 형식의 CSV 형식이 있습니다. * |영구 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |... url ... |알 수 없는 오류 발생: ' System. Exception ' 형식의 예외가 throw 되었습니다. |Transient |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |... url ... |Blob을 다운로드 하지 못했습니다. 클라이언트가 지정 된 시간 제한 내에 작업을 완료할 수 없습니다. |영구 |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0
