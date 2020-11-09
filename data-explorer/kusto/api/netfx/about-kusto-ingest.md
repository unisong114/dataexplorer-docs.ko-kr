---
title: 클라이언트 라이브러리를 수집 하는 kusto 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 수집 클라이언트 라이브러리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: 33d3515a8465a1e9c3397e675a51c95aa3f00d42
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373836"
---
# <a name="kusto-ingest-client-library"></a>Kusto 수집 클라이언트 라이브러리 

`Kusto.Ingest` 라이브러리는 Kusto 서비스로 데이터를 전송 하기 위한 .NET 4.6.2 라이브러리입니다.
다음 라이브러리와 Sdk에 대 한 종속성을 가져옵니다.

* Azure AD 인증용 ADAL
* Azure storage 클라이언트

수집 메서드는 [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) 인터페이스에 의해 정의 됩니다.  메서드는 동기 모드와 비동기 모드의 스트림, IDataReader, 로컬 파일 및 Azure blob에서 데이터 수집을 처리 합니다.

## <a name="ingest-client-flavors"></a>수집 클라이언트 특색

수집 클라이언트의 두 가지 기본 특성은 대기 및 직접입니다.

### <a name="queued-ingestion"></a>대기 중인 큐 수집

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)에 의해 정의 되는 대기 중인 수집 모드는 Kusto 서비스에 대 한 클라이언트 코드 종속성을 제한 합니다. 수집은 kusto 수집 메시지를 Azure 큐에 게시 한 후 Kusto 데이터 관리 (수집) 서비스에서 획득 하 여 수행 됩니다. 모든 중간 저장소 항목은 Kusto 데이터 관리 서비스의 리소스를 사용 하 여 수집 클라이언트에서 만들어집니다.

**대기 모드의 장점은 다음과 같습니다.**

* Kusto Engine 서비스에서 데이터 수집 프로세스 분리
* Kusto 엔진 (또는 수집) 서비스를 사용할 수 없는 경우 수집 요청을 지속할 수 있습니다.
* 수집 서비스에서 인바운드 데이터를 효율적이 고 제어 가능한 방식으로 집계 하 여 성능을 향상 시킵니다. 
* Kusto 수집 서비스에서 Kusto Engine 서비스의 수집 부하를 관리할 수 있습니다.
* XStore 제한 등의 일시적인 수집 오류에서 필요에 따라 Kusto 수집 서비스를 다시 시도 합니다.
* 모든 수집 요청의 진행률과 결과를 추적 하는 편리한 메커니즘을 제공 합니다.

다음 다이어그램은 Kusto와의 대기 중인 수집 클라이언트 상호 작용을 간략하게 설명 합니다.

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="쿼리 된 수집 모드에서 kusto. 수집 라이브러리가 쿼리를 Kusto 서비스로 보내는 방법을 보여 주는 다이어그램입니다.":::
 
### <a name="direct-ingestion"></a>직접 수집

IKustoDirectIngestClient에 의해 정의 되는 직접 수집 모드는 Kusto Engine 서비스와 직접 상호 작용을 강제로 수행 합니다. 이 모드에서 Kusto 수집 서비스는 데이터를 보통 또는 관리 하지 않습니다. 모든 수집 요청은 결국 `.ingest` Kusto Engine 서비스에서 직접 실행 되는 명령으로 변환 됩니다.

다음 다이어그램은 Kusto와의 직접 수집 클라이언트 상호 작용을 간략하게 설명 합니다.

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="Kusto. 수집 라이브러리가 직접 수집 모드에서 Kusto 서비스로 쿼리를 전송 하는 방법을 보여 주는 다이어그램입니다.":::

> [!NOTE]
> 프로덕션 등급 수집 솔루션에는 직접 모드를 권장 하지 않습니다.

**직접 모드의 장점은 다음과 같습니다.**

* 대기 시간이 짧고 집계가 없습니다. 그러나 대기 시간이 짧은 수집을 사용 하 여 대기 시간이 길어질 수도 있습니다.
* 동기 메서드를 사용 하는 경우 메서드 완료는 수집 작업의 끝을 나타냅니다.

**직접 모드의 단점은 다음과 같습니다.**

* 클라이언트 코드는 다시 시도 또는 오류 처리 논리를 구현 해야 합니다.
* Kusto Engine 서비스를 사용할 수 없는 경우 Ingestions는 불가능 합니다.
* 클라이언트 코드는 엔진 서비스 용량을 인식 하지 못하므로 수집 요청을 사용 하 여 Kusto Engine 서비스에 과부하가 걸릴 수 있습니다.

## <a name="ingestion-best-practices"></a>수집 모범 사례

수집 [모범 사례](kusto-ingest-best-practices.md) 는 수집에 대 한 COGS (판매 상품 비용) 및 처리량 POV를 제공 합니다.

* **스레드 보안-** Kusto 수집 클라이언트 구현은 스레드로부터 안전 하 고 다시 사용 하기 위한 것입니다. `KustoQueuedIngestClient`각 또는 여러 수집 작업에 대해 클래스의 인스턴스를 만들 필요가 없습니다. `KustoQueuedIngestClient`사용자 프로세스별 클러스터에서 대상 Kusto 클러스터에는 단일 인스턴스가 필요 합니다. 여러 인스턴스를 실행 하는 것은 생산성이 높으며 데이터 관리 클러스터에서 DoS를 발생 시킬 수 있습니다.

* **지원 되는 데이터 형식-** 네이티브 수집을 사용 하는 경우, 아직 없는 경우 하나 이상의 Azure storage blob에 데이터를 업로드 합니다. 현재 지원 되는 blob 형식은 지원 되는 [데이터 형식](../../../ingestion-supported-formats.md)아래에 설명 되어 있습니다.

* **스키마 매핑-** 
 [스키마 매핑은](../../management/mappings.md) 원본 데이터 필드를 대상 테이블 열에 명확 하 게 바인딩하는 데 도움이 됩니다.

* 수집 **권한-** 
 [Kusto 수집 권한은](kusto-ingest-client-permissions.md) 패키지를 사용 하 여 성공적으로 수집 하는 데 필요한 권한 설정에 대해 설명 `Kusto.Ingest` 합니다.

* **사용량-** 앞에서 설명한 것 처럼 Kusto에 대 한 유지 가능 하 고 확장성이 뛰어난 수집 솔루션에 대 한 권장 되는 기준은 **KustoQueuedIngestClient** 여야 합니다.
Kusto 서비스에 대 한 불필요 한 로드를 최소화 하려면 단일 kusto를 사용 하 여 Kusto 클러스터 별로 클라이언트 (큐에 대기 또는 직접)를 수집 하는 것이 좋습니다. Kusto 수집 클라이언트 구현은 스레드로부터 안전 하 고 완전히 재진입입니다.

## <a name="next-steps"></a>다음 단계

* [Kusto. 수집 클라이언트 참조](kusto-ingest-client-reference.md) 에는 클라이언트 인터페이스 및 구현을 수집 하는 kusto 완전 한 참조가 포함 되어 있습니다. 수집 클라이언트를 만들고 수집 요청을 보강 하 고 수집 진행률을 관리 하는 방법에 대 한 정보를 찾을 수 있습니다.

* [Kusto. 수집 작업 상태는 수집](kusto-ingest-client-status.md) 상태 추적을 위한 KustoQueuedIngestClient 기능을 설명 합니다.

* [Kusto. 수집 오류](kusto-ingest-client-errors.md) 는 kusto 수집 클라이언트 오류 및 예외를 설명 합니다.

* [Kusto. 수집 예제](kusto-ingest-client-examples.md) 에서는 데이터를 kusto로 수집 하는 다양 한 기술을 보여 주는 코드 조각을 보여 줍니다.

* [Kusto. 수집 라이브러리 없이 데이터를 수집](kusto-ingest-client-rest.md) 하는 경우 KUSTO REST api를 사용 하 고 라이브러리에 종속 되지 않고 대기 중인 Kusto 수집을 구현 하는 방법을 설명 합니다. `Kusto.Ingest`

