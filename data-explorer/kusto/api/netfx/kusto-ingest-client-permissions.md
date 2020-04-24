---
title: Kusto.Ingest 참조 - 인기 권한 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Kusto.Ingest 참조 - Azure 데이터 탐색기의 수집 권한에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502893"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto.Ingest 참조 - 인기 권한
이 문서에서는 `Native` 사용이 작동하기 위해 서비스에 설정해야 하는 사용 권한을 설명합니다.



## <a name="prerequisites"></a>사전 요구 사항
* 이 문서에서는 [Kusto 제어 명령을 사용하여 Kusto](../../management/security-roles.md) 서비스 및 데이터베이스에서 권한 부여 설정을 보고 수정하는 방법을 설명합니다.
* 다음 AAD 응용 프로그램은 아래 예제에서 샘플 주체로 사용됩니다.
    * 테스트 AAD 앱 (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * 쿠스토 내부 섭취 AAD 앱 (76263cdb-1234-5678-9012-545644e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>큐에 대기된 인시징에 대한 인스레이션 권한 모델
[IKustoQueuedIngestClient에](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)정의된 이 모드는 Kusto 서비스에 대한 클라이언트 코드 종속성을 제한합니다. 수집은 Kusto 수집 메시지를 Azure 큐에 게시하여 수행되며, 이 메시지는 Kusto 데이터 관리(일명 Kusto Data Management)에서 획득합니다. 섭취) 서비스. 모든 중간 저장소 아티팩트는 Kusto 데이터 관리 서비스에서 할당한 리소스를 사용하여 인제스트 클라이언트에 의해 생성됩니다.<BR>

다음 다이어그램에서는 Kusto와의 큐에 대기된 수기 클라이언트 상호 작용을 간략하게 설명합니다.<BR>

![대체 텍스트](../images/queued-ingest.jpg "큐에 대기된 인제스트")

### <a name="permissions-on-the-engine-service"></a>엔진 서비스에 대한 권한
데이터베이스에서 `T1` `DB1` 테이블로 데이터를 수집할 수 있는 자격을 얻으려면 수집 작업을 수행하는 주 서버가 승인되어야 합니다.
필요한 최소 권한 `Database Ingestor` `Table Ingestor` 수준은 데이터베이스의 모든 기존 테이블또는 특정 기존 테이블에 데이터를 수집할 수 있습니다.
테이블 만들기가 `Database User` 필요하거나 더 높은 액세스 역할도 할당해야 하는 경우


|역할 |PrincipalType    |보안 주체 표시 이름
|--------|------------|------------
|데이터베이스 *** 인제스토르 |AAD 응용 프로그램 |테스트 앱(앱 ID: 2a904276-1234-5678-9012-66fc53add60b)
|표 *** 인기스토르 |AAD 응용 프로그램 |테스트 앱(앱 ID: 2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`주체(Kusto 내부 수집 앱)는 `Cluster Admin` 역할에 불변하게 매핑되므로 Kusto가 관리하는 수집 파이프라인에서 일어나는 모든 테이블에 데이터를 수집할 수 있는 권한이 부여됩니다.

데이터베이스 `DB1` 또는 테이블에 `T1` 필요한 권한을 AAD `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` 앱에 부여하는 것은 다음과 같습니다.
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

