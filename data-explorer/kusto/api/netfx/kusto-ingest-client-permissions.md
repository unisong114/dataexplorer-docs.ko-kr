---
title: Kusto. 수집 참조 수집 권한-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Kusto. Azure 데이터 탐색기의 참조 수집 사용 권한을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617921"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto. 수집 참조 수집 권한
이 문서에서는 수집 작업을 위해 `Native` 서비스에 설정 하는 데 필요한 권한을 설명 합니다.


## <a name="prerequisites"></a>전제 조건
* Kusto 서비스 및 데이터베이스에서 권한 부여 설정을 보고 수정 하려면 [kusto 제어 명령을](../../management/security-roles.md) 참조 하세요. 

## <a name="references"></a>참조
* 다음 AAD 응용 프로그램은 아래 예제에서 샘플 보안 주체로 사용 됩니다.
    * AAD 앱 테스트 (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Kusto 내부 수집 AAD 앱 (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>큐에 있는 수집에 대 한 수집 권한 모델
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)에 정의 된이 모드는 Azure 데이터 탐색기 서비스에 대 한 클라이언트 코드 종속성을 제한 합니다. 수집은 Kusto 수집 메시지를 Azure 큐에 게시 하 여 수행 됩니다. Azure 데이터 탐색기 서비스에서 큐를 가져옵니다. It'ss (수집 서비스 라고도 함)  Azure 데이터 탐색기 서비스에서 할당 한 리소스를 사용 하 여 수집 클라이언트에서 모든 중간 저장소 아티팩트를 만듭니다.

다음 다이어그램은 Kusto와의 대기 중인 수집 클라이언트 상호 작용을 간략하게 설명 합니다.<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="대기 중인 수집":::

### <a name="permissions-on-the-engine-service"></a>엔진 서비스에 대 한 사용 권한
데이터베이스 `T1` `DB1`의 테이블에 데이터를 수집 하려면 수집 작업을 수행 하는 보안 주체에 게 권한이 있어야 합니다.
필요한 최소 권한 수준은 `Database Ingestor` 이며, `Table Ingestor` 데이터베이스의 모든 기존 테이블 또는 특정 기존 테이블로 데이터를 수집할 수 있습니다.
테이블을 `Database User` 만드는 데 필요한 경우 또는 더 높은 액세스 역할도 할당 해야 합니다.


|역할 |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|`Database Ingestor` |AAD 응용 프로그램 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |AAD 응용 프로그램 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Kusto 내부 수집 앱 인 보안 주체는 immutably `Cluster Admin` 역할에 매핑되고 데이터를 테이블에 수집할 수 있는 권한이 부여 됩니다. 이는 Kusto 관리 수집 파이프라인에서 일어나는 일입니다.

데이터베이스 `DB1` 또는 테이블 `T1` 에 대 한 필수 권한을 AAD 앱 `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` 에 부여 하는 것은 다음과 같습니다.
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
