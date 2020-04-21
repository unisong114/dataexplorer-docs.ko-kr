---
title: Kusto 인제스트 클라이언트 라이브러리 - 모범 사례 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Kusto 인제스트 클라이언트 라이브러리 - Azure 데이터 탐색기의 모범 사례에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: e28ec3f99abe4163b83721d753da98c0035d1e46
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523701"
---
# <a name="kusto-ingest-client-library---best-practices"></a>쿠스토 인제스트 클라이언트 라이브러리 - 모범 사례

## <a name="choosing-the-right-ingestclient-flavor"></a>올바른 인제스트클라이언트 맛 선택
[KustoQueuedingestClient를](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 사용하는 것이 권장되는 기본 데이터 수집 모드입니다. 이유는 다음과 같습니다.
* Kusto Engine 가동 중지 시간(예: 배포 중)에는 직접 수집이 불가능하지만 큐에 대기중인 수집 모드에서는 요청이 Azure 큐로 유지되고 필요에 따라 데이터 관리 서비스가 다시 시도됩니다.
* 데이터 관리 서비스는 수집 요청으로 엔진에 과부하가 걸리지 않도록 해야 합니다. 이 컨트롤을 재정의하면(예: 직접 사용) 엔진 성능에 심각한 영향을 줄 수 있습니다.
* 데이터 관리는 여러 수집 요청을 집계하여 생성할 초기 샤드(익스텐츠)의 크기를 최적화합니다.
* 성공 여부에 관계없이 각 섭취에 대한 피드백을 얻을 수있는 편리한 방법이 있습니다.

## <a name="tracking-ingest-operation-status"></a>인제스트 작업 상태 추적
[인베이스트 작업 상태를 추적하는](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) 것은 Kusto에서 유용한 기능이지만 성공 보고를 위해 켜면 서비스가 무력화될 정도로 쉽게 악용될 수 있습니다.<BR>

> [!WARNING]
> 대용량 데이터 스트림에 대한 모든 수집 요청에 대해 긍정적인 알림을 켜는 것은 기본 xStore 리소스에 극단적인 부하를 주므로 > 수집 대기 시간이 증가하고 클러스터 응답성이 완전해질 수 있습니다.

## <a name="optimizing-for-throughput"></a>처리량 최적화
* 수집은 가장 잘 작동합니다(즉, 수집 중에 최소한의 리소스를 소비하고, COGS에 최적화된 데이터 샤드를 생성하며, 큰 청크에서 수행하면 가장 성능이 좋은 데이터 아티팩트가 생성됩니다). 일반적으로 Kusto.Ingest 라이브러리를 사용하거나 엔진에 직접 데이터를 수집하여 **100MB에서 1GB(압축되지 않은)의** 일괄 처리로 데이터를 보내는 것이 좋습니다.
* 상한은 Kusto 엔진으로 직접 작업하여 수집 프로세스에서 사용되는 메모리 양을 줄이는 데 `KustoQueuedIngestClient` 도움이 되는 경우 중요합니다(클래스를 사용할 때 지나치게 큰 데이터 블록은 클라이언트에서 더 작은 부분으로 분할되고 작은 청크는 Kusto Engine에 도달하기 전에 어느 정도 집계됩니다).
* 덜 중요하지만 수집된 데이터 크기에 대한 하한도 중요합니다. 대량 배치를 사용하는 것보다 약간 덜 효율적이지만 때때로 작은 배치로 데이터를 수집하면 완벽하게 괜찮습니다. `KustoQueuedIngestClient`클래스는 또한 많은 양의 데이터를 수집해야하고 Kusto로 보내기 전에 큰 청크로 배치 할 수없는 고객의 문제를 해결합니다.

## <a name="factors-impacting-ingestion-throughput"></a>섭취 처리량에 영향을 미치는 요인
여러 요인이 섭취 처리량에 영향을 줄 수 있습니다. Kusto 의 섭취 파이프라인을 계획할 때 COG에 중요한 영향을 미칠 수 있는 다음 사항을 평가해야 합니다.
* 데이터 형식 - CSV는 가장 빠른 형식이며, JSON은 일반적으로 동일한 양의 데이터에 대해 x2 또는 x3이 더 오래 소요됩니다.
* 테이블 너비 - 테이블이 넓을수록 더 많은 열이 인코딩되고 인덱싱되므로 처리량이 낮아질수록 실제로 필요한 데이터만 수집해야 합니다.
    인시던션 매핑을 제공하여 인제스트할 필드를 제어할 수 있습니다.
* 소스 데이터 위치 - 영역 간 읽기 방지로 수집 속도가 빨라집니다.
* 클러스터로드 - 클러스터가 높은 쿼리 부하를 경험하는 경우, 인제는 완료하는 데 더 오래 걸리며 처리량감소
* 섭취 패턴 - 클러스터가 최대 1GB의 일괄 처리와 함께 제공될 때 섭취가 최적입니다(사용하여 `KustoQueuedIngestClient`관리).

## <a name="optimizing-for-cogs"></a>COGS에 대한 최적화
Kusto에 데이터를 수집하기 위해 Kusto 클라이언트 라이브러리를 사용하는 것이 가장 저렴하고 가장 강력한 옵션으로 남아 있지만, 우리는 고객이 수집 전술을 검토하고 Blob 트랜잭션을 크게 (~x100) 더 비싸게 만든 Azure Storage 가격의 최근(2017년 가을) 변경 사항을 고려할 것을 촉구합니다.
<BR>
Kusto에 보내는 데이터(파일/Blob/streams)가 많을수록 월별 청구서가 커집니다.
다음 권장 사항을 따르면 Kusto 의 사용 비용을 더 잘 제어할 수 있습니다.
* **더 큰 데이터 청크를 수집하는 것을 선호합니다(최대 1GB의 압축되지 않은 데이터)**<br>
    많은 팀이 수천만 개의 작은 데이터 덩어리를 수집하여 대기 시간을 줄이려고 시도하는데, 이는 매우 비효율적이고 비용이 많이 듭니다.<br>
    클라이언트 측의 모든 일괄 처리가 도움이 될 것입니다. 
* **Kusto.Ingest 클라이언트에 압축되지 않은 정확한 데이터 크기를 제공해야 합니다.**<br>
    이렇게 하지 않을 경우 Kusto가 추가 저장소 트랜잭션을 수행할 수 있습니다.
* `FlushImmediately` 을 사용하여 수집할 데이터를 보내거나 `true` `ingest-by` / `drop-by` 태그가 설정된 작은 청크를 보내지 **마십시오.**<br>
    이를 사용하면 Kusto 서비스가 수집 중에 데이터를 제대로 집계하지 못하게 되고 수집 후 불필요한 저장소 트랜잭션이 발생하여 COGS에 영향을 미칩니다.<br>
    또한 이러한 과도하게 사용하면 클러스터의 사용 및/또는 쿼리 성능이 저하될 수 있습니다.<br>
    