---
title: 클라이언트 라이브러리를 수집 하는 kusto 수집-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 클라이언트 라이브러리를 수집 하는 Kusto를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8357bc387ae26fc0dfcc50c5e712a5b39df32942
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799716"
---
# <a name="kusto-ingest-client-library"></a>Kusto 수집 클라이언트 라이브러리

## <a name="overview"></a>개요
Kusto. 수집 라이브러리는 Kusto 서비스로 데이터를 보낼 수 있도록 하는 .NET 4.6.2 라이브러리입니다.
Kusto. 수집은 다음 라이브러리 및 Sdk에 대 한 종속성을 갖습니다.

* AAD 인증용 ADAL
* Azure Storage 클라이언트

Kusto 수집 메서드는 [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) 인터페이스에 의해 정의 되며, 동기 및 비동기 모드에서 Stream, IDataReader, 로컬 파일 및 Azure blob에서 데이터 수집을 허용 합니다.

## <a name="ingest-client-flavors"></a>수집 클라이언트 특색
개념적으로 수집 클라이언트의 두 가지 기본 특성은 대기 및 직접입니다.

### <a name="queued-ingestion"></a>대기 중인 수집
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)에 의해 정의 된이 모드는 Kusto 서비스에 대 한 클라이언트 코드 종속성을 제한 합니다. 수집은 kusto 수집 메시지를 Azure 큐에 게시 하 여 수행 합니다 .이 메시지는 Kusto 데이터 관리 (수집) 서비스에서 가져옵니다. 모든 중간 저장소 아티팩트는 Kusto 데이터 관리 서비스에서 할당 한 리소스를 사용 하 여 수집 클라이언트에서 만듭니다.

**대기 모드의 장점은** 다음과 같이 제한 되지 않습니다.

* Kusto Engine 서비스에서 데이터 수집 프로세스 분리
* Kusto 엔진 (또는 수집) 서비스를 사용할 수 없는 경우 수집 요청을 지속할 수 있습니다.
* 수집 서비스에서 인바운드 데이터를 효율적이 고 제어 가능한 방식으로 집계할 수 있으므로 성능을 향상 시킬 수 있습니다.
* Kusto 수집 서비스에서 Kusto Engine 서비스의 수집 부하를 관리할 수 있습니다.
* Kusto 수집 서비스는 일시적인 수집 오류 (예: XStore 제한)에서 필요에 따라 다시 시도 합니다.
* 모든 수집 요청의 진행률과 결과를 추적 하는 편리한 메커니즘을 제공 합니다.

다음 다이어그램은 Kusto와의 대기 중인 수집 클라이언트 상호 작용을 간략하게 설명 합니다.

![대체 텍스트](../images/queued-ingest.jpg "대기 중인 수집")

### <a name="direct-ingestion"></a>직접 수집
IKustoDirectIngestClient에 의해 정의 된이 모드는 Kusto Engine 서비스와 직접 상호 작용 합니다. 이 모드에서 Kusto 수집 서비스는 데이터를 보통 또는 관리 하지 않습니다. 직접 모드의 모든 수집 요청은 결국 Kusto `.ingest` Engine 서비스에서 직접 실행 되는 명령으로 변환 됩니다.
다음 다이어그램은 Kusto와의 직접 수집 클라이언트 상호 작용을 간략하게 설명 합니다.

![대체 텍스트](../images/direct-ingest.jpg "직접 수집")

> [!NOTE]
> 프로덕션 등급 수집 솔루션에는 직접 모드를 사용할 수 없습니다.

**직접 모드의 장점은** 다음과 같습니다.

* 짧은 대기 시간 (집계가 없음) 그러나 대기 시간이 짧은 수집을 사용 하 여 대기 시간이 길어질 수도 있습니다.
* 동기 메서드를 사용 하는 경우 메서드 완료는 수집 작업의 끝을 나타냅니다.

**직접 모드의 단점은** 다음과 같습니다.

* 클라이언트 코드는 다시 시도 또는 오류 처리 논리를 구현 해야 합니다.
* Kusto Engine 서비스를 사용할 수 없는 경우 Ingestions는 불가능 합니다.
* 클라이언트 코드는 엔진 서비스 용량을 인식 하지 못하므로 수집 요청을 사용 하 여 Kusto Engine 서비스에 과부하가 걸릴 수 있습니다.

## <a name="ingestion-best-practices"></a>수집 모범 사례

### <a name="general"></a>일반
수집 [모범 사례](kusto-ingest-best-practices.md) 는 수집에 COGs 및 처리량 POV를 제공 합니다.

### <a name="thread-safety"></a>스레드로부터의 안전성
Kusto 수집 클라이언트 구현은 스레드로부터 안전 하 고 다시 사용 하기 위한 것입니다. 각 또는 여러 수집 작업에 대해 클래스의 `KustoQueuedIngestClient` 인스턴스를 만들 필요가 없습니다. 사용자 프로세스별 클러스터에서 `KustoQueuedIngestClient` 대상 Kusto 클러스터에는 단일 인스턴스가 필요 합니다. 여러 인스턴스를 실행 하는 것은 생산적 이며 데이터 관리 클러스터를 DoS 할 수 있습니다.

### <a name="supported-data-formats"></a>지원 되는 데이터 형식
네이티브 수집을 사용 하는 경우 해당 데이터를 하나 이상의 Azure Storage blob에 업로드 합니다. 현재 지원 되는 blob 형식은 지원 되는 [데이터 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) 항목에 설명 되어 있습니다.

### <a name="schema-mapping"></a>스키마 매핑
[스키마 매핑은](../../management/mappings.md) 원본 데이터 필드를 대상 테이블 열에 명확 하 게 바인딩하는 데 도움이 됩니다.

## <a name="usage-and-further-reading"></a>사용량 및 추가 정보

* 위에서 설명한 것 처럼 Kusto에 대해 유지 가능 하 고 높은 확장성 수집 솔루션에 대해 권장 되는 기준은 **KustoQueuedIngestClient**여야 합니다.
* Kusto 서비스에 대 한 불필요 한 로드를 최소화 하려면 Kusto 클러스터 당 프로세스 당 Kusto 수집 클라이언트 (큐에 대기 또는 직접)의 단일 인스턴스를 사용 하는 것이 좋습니다. Kusto 수집 클라이언트 구현은 스레드로부터 안전 하 고 완전히 재진입입니다.

### <a name="ingestion-permissions"></a>수집 권한
* [Kusto 수집 권한은](kusto-ingest-client-permissions.md) kusto. 수집 패키지를 사용 하 여 성공적으로 수집 하는 데 필요한 권한 설정에 대해 설명 합니다.

### <a name="kustoingest-library-reference"></a>Kusto. 수집 라이브러리 참조
* [Kusto. 수집 클라이언트 참조](kusto-ingest-client-reference.md) 에는 클라이언트 인터페이스 및 구현을 수집 하는 kusto 완전 한 참조가 포함 되어 있습니다.<BR>수집 클라이언트를 만들고 수집 요청을 보강 하 고 수집 진행률을 관리 하는 방법에 대 한 정보를 찾을 수 있습니다.
* [Kusto. 수집 작업 상태는 수집](kusto-ingest-client-status.md) 상태 추적을 위한 **KustoQueuedIngestClient** 기능을 설명 합니다.
* [Kusto. 수집 오류](kusto-ingest-client-errors.md) 문서 Kusto 수집 클라이언트 오류 및 예외 수집
* [Kusto. 수집 예제에서는](kusto-ingest-client-examples.md) 데이터를 kusto로 수집 하는 다양 한 기술을 보여 주는 코드 조각을 제공 합니다.

### <a name="data-ingestion-rest-apis"></a>데이터 수집 REST Api
[Kusto. 수집 라이브러리 없이 데이터를 수집](kusto-ingest-client-rest.md) 하는 경우 KUSTO REST api를 사용 하 여 수집 하 고 kusto. 수집 라이브러리에 종속성을 적용 하지 않고도 대기 중인 Kusto 수집을 구현 하는 방법

