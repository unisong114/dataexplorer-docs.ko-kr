---
title: Kusto 인제스트 클라이언트 라이브러리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto 인제스트 클라이언트 라이브러리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0fd86579f4ca6471903305ca9249b78bc03b8cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503131"
---
# <a name="kusto-ingest-client-library"></a>쿠스토 인제스트 클라이언트 라이브러리

## <a name="overview"></a>개요
Kusto.Ingest 라이브러리는 Kusto 서비스로 데이터를 전송할 수 있는 .NET 4.6.2 라이브러리입니다.
Kusto.Ingest는 다음 라이브러리 및 SDK에 대한 종속성을 사용합니다.

* AAD 인증을 위한 ADAL
* Azure 저장소 클라이언트
* [TBD: 외부 종속성의 전체 목록]

Kusto 수집 방법은 [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) 인터페이스에 의해 정의되며 동기 및 비동기 모드에서 스트림, IDataReader, 로컬 파일 및 Azure Blob(들)의 데이터 수집을 허용합니다.

## <a name="ingest-client-flavors"></a>고객 의 맛을 섭취
개념적으로 인제스트 클라이언트의 두 가지 기본 맛은 큐에 대기 및 직접입니다.

### <a name="queued-ingestion"></a>큐에 대기된 인시징
[IKustoQueuedIngestClient에](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)의해 정의 된이 모드는 Kusto 서비스에 대한 클라이언트 코드 종속성을 제한합니다. 수집은 Kusto 데이터 관리(섭취) 서비스에서 획득한 Azure 큐에 Kusto 수집 메시지를 게시하여 수행됩니다. 모든 중간 저장소 아티팩트는 Kusto 데이터 관리 서비스에서 할당한 리소스를 사용하여 인제스트 클라이언트에 의해 생성됩니다.

**큐에 대기 모드의 장점은** 다음과 같습니다(이에 국한되지 않음).

* Kusto 엔진 서비스에서 데이터 수집 프로세스의 분리
* Kusto Engine(또는 인베이션) 서비스를 사용할 수 없는 경우 인베이션 요청을 계속 사용할 수 있습니다.
* 인바운드 서비스를 통해 인바운드 데이터를 효율적이고 제어 가능한 집계할 수 있어 성능 향상
* Kusto 엔진 서비스에서 섭취 부하를 관리할 수 있는 Kusto 섭취 서비스
* Kusto 인기 변환 서비스는 일시적인 섭취 실패(예: XStore 스로틀링)에 필요에 따라 다시 시도합니다.
* 모든 섭취 요청의 진행 상황과 결과를 추적하는 편리한 메커니즘 제공

다음 다이어그램에서는 Kusto와의 큐에 대기된 수기 클라이언트 상호 작용을 간략하게 설명합니다.

![대체 텍스트](../images/queued-ingest.jpg "큐에 대기된 인제스트")

### <a name="direct-ingestion"></a>직접 섭취
IKustoDirectIngestClient에 의해 정의된 이 모드는 Kusto 엔진 서비스와 직접 상호 작용합니다. 이 모드에서 Kusto 인비온레이션 서비스는 데이터를 조정하거나 관리하지 않습니다. 직접 모드에서 모든 섭취 요청은 결국 Kusto 엔진 서비스에서 직접 실행되는 `.ingest` 명령으로 변환됩니다.
다음 다이어그램에서는 Kusto와의 직접 섭취 클라이언트 상호 작용에 대해 간략하게 설명합니다.

![대체 텍스트](../images/direct-ingest.jpg "직접 섭취")

> [!NOTE]
> 직접 모드는 프로덕션 등급 의 섭취 솔루션에는 권장되지 않습니다.

**직접 모드의 장점은** 다음과 같습니다.

* 대기 시간이 적다(집계 없음). 그러나 큐에 대기된 섭취를 통해 짧은 대기 시간을 달성할 수도 있습니다.
* 동기 메서드를 사용하는 경우 메서드 완료는 인기 작업의 끝을 나타냅니다.

**직접 모드의 단점은** 다음과 같습니다.

* 클라이언트 코드는 재시도 또는 오류 처리 논리를 구현해야 합니다.
* 쿠스토 엔진 서비스를 이용할 수 없는 경우 섭취가 불가능합니다.
* 클라이언트 코드는 엔진 서비스 용량을 인식하지 못하므로 Kusto Engine 서비스를 인기 요청으로 압도할 수 있습니다.

## <a name="ingestion-best-practices"></a>섭취 모범 사례

### <a name="general"></a>일반
[섭취 모범 사례는](kusto-ingest-best-practices.md) GG및 섭취시 처리량 POV를 제공합니다.

### <a name="thread-safety"></a>스레드로부터의 안전성
Kusto 인제스트 클라이언트 구현은 스레드가 안전하며 다시 사용할 수 있도록 의도되어 있습니다. 각 또는 여러 인제스트 `KustoQueuedIngestClient` 작업에 대해 클래스 인스턴스를 만들 필요가 없습니다. 사용자 프로세스당 `KustoQueuedIngestClient` 대상 Kusto 클러스터당 단일 인스턴스가 필요합니다. 여러 인스턴스를 실행하는 것은 생산성이 떨어지고 데이터 관리 클러스터를 DoS로 수행할 수 있습니다.

### <a name="supported-data-formats"></a>지원되는 데이터 형식
네이티브 수집을 사용하는 경우 아직 없는 경우 하나 이상의 Azure Storage Blob에 데이터를 업로드합니다. 현재 지원되는 Blob 형식은 지원되는 데이터 형식 항목에 [설명되어 있습니다.](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)

### <a name="schema-mapping"></a>스키마 매핑
[스키마 매핑은](../../management/mappings.md) 원본 데이터 필드를 대상 테이블 열에 결정적으로 바인딩하는 데 도움이 됩니다.

## <a name="usage-and-further-reading"></a>사용 및 추가 읽기

* 위에서 설명한 것처럼 Kusto의 지속 가능하고 대규모 인기 솔루션에 대한 권장 기준은 **KustoQueuedingestClient이어야**합니다.
* Kusto 서비스에 대한 불필요한 로드를 최소화하려면 Kusto 클러스터당 프로세스당 Kusto Ingest 클라이언트(큐또는 직접)의 단일 인스턴스를 사용하는 것이 좋습니다. Kusto Ingest 클라이언트 구현은 스레드 안전하고 완전히 재진입합니다.

### <a name="ingestion-permissions"></a>인비전에이션 권한
* [Kusto 섭취 권한은](kusto-ingest-client-permissions.md) Kusto.Ingest 패키지를 사용하여 성공적인 섭취에 필요한 사용 권한 설정에 대해 설명합니다.

### <a name="kustoingest-library-reference"></a>쿠스토.인제스트 라이브러리 레퍼런스
* [Kusto.Ingest 클라이언트 참조는](kusto-ingest-client-reference.md) Kusto 인제스트 클라이언트 인터페이스 및 구현에 대한 완전한 참조를 포함합니다.<BR>거기에서 인제스트 클라이언트를 만드는 방법, 수집 요청을 보강하고 수집 진행 률 을 관리하는 방법에 대한 정보를 찾을 수 있습니다.
* [Kusto.Ingest 작업 상태는](kusto-ingest-client-status.md) 섭취 상태를 추적하기 위한 **KustoQueuedingestClient** 시설에 대해 설명합니다.
* [Kusto.Ingest 오류](kusto-ingest-client-errors.md) 문서 Kusto 인제스트 클라이언트 오류 및 예외
* [Kusto.Ingest 예제는 Kusto에](kusto-ingest-client-examples.md) 데이터를 수집하는 다양한 기술을 보여주는 코드 조각을 제공합니다.

### <a name="data-ingestion-rest-apis"></a>데이터 수집 REST API
[Kusto.Ingest 라이브러리가 없는 데이터 수집은 Kusto](kusto-ingest-client-rest.md) REST API를 사용하고 Kusto.Ingest 라이브러리에 의존하지 않고 큐에 있는 Kusto 수집을 구현하는 방법을 설명합니다.

