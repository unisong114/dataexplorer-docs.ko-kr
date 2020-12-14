---
title: Azure 데이터 탐색기 Kusto EngineV3 (미리 보기)
description: Azure 데이터 탐색기 (Kusto) EngineV3에 대 한 자세한 정보
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 133f01498d4cf430d7fdc2649df88186610b3954
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388973"
---
# <a name="enginev3---preview"></a>EngineV3-미리 보기

Kusto EngineV3는 Azure 데이터 탐색기의 차세대 저장소 및 쿼리 엔진입니다. 원격 분석, 로그 및 시계열 데이터를 수집 하 고 쿼리 하는 데 탁월한 성능을 제공 하도록 설계 되었습니다.

EngineV3에는 새로운 최적화 된 저장소 형식 및 인덱스가 포함 되어 있습니다. EngineV3는 고급 데이터 통계 쿼리 최적화를 사용 하 여 최적의 쿼리 계획 및 just-in-time 컴파일 쿼리 실행을 만듭니다. 또한 EngineV3는 향상 된 디스크 캐시를 사용 하 여 쿼리 성능을 향상 시켜 현재 엔진 (EngineV2)에 비해 크기를 향상 시킵니다. EngineV3는 Azure 데이터 탐색기 서비스의 다음 혁신 혁신을 위한 토대를 마련 했습니다.

EngineV3 모드로 실행 중인 Azure 데이터 탐색기 클러스터는 EngineV2와 완전히 호환 되므로 데이터 마이그레이션은 필요 하지 않습니다.

> [!IMPORTANT]
> EngineV3은 다음 단계에서 사용할 수 있습니다.
>
> 1. 공개 미리 보기 (현재 상태): 사용자가 EngineV3 모드에서 새 클러스터를 만들 수 있습니다. 공개 미리 보기 기간 중에는 클러스터가 SLA를 적용 하지 않으며 Azure 데이터 탐색기 태그에 대해 요금이 청구 되지 않습니다. 인프라 비용은 평소와 같이 청구 됩니다.
> 1. GA (일반 공급): 기본적으로 모든 새 클러스터가 EngineV3 모드에서 만들어집니다. SLA는 모든 EngineV3 및 EngineV2 프로덕션 클러스터에 적용 됩니다.
> 1. GA 게시: EngineV2에서 실행 되는 기존 작업은 EngineV3로 마이그레이션됩니다. Azure 데이터 탐색기 태그 요금이 다시 시작 됩니다.

## <a name="how-enginev3-works"></a>EngineV3 작동 방식

EngineV3는 기존 열 저장소 (EngineV2) 및 행 저장소 (스트리밍 수집에 사용 됨)와 병렬로 실행 되는 추가 열 저장소 저장소 엔진입니다. 테이블은 세 저장소의 데이터를 한 번에 모두 통합할 수 있으며,이 "페더레이션" 데이터는 사용자 관점에서 투명 합니다.

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Azure 데이터 탐색기/Kusto EngineV3 아키텍처의 도식 표현":::

테이블에 수집 되는 모든 데이터는 테이블의 가로 조각 인 분할로 분할 됩니다. 각 분할 된 레코드는 일반적으로 몇 백만 개의 레코드를 포함 하며 다른 분할와 독립적으로 인코딩되고 인덱싱됩니다. 이 기능을 사용 하 여 엔진은 수집 처리량의 선형 규모를 달성할 수 있습니다.

분할는 클러스터 노드 전체에 고르게 분산 되며 로컬 SSD와 메모리 모두에 캐시 됩니다. 쿼리 계획자와 쿼리 엔진은 이러한 분할 된 배포 및 캐싱을 활용 하는 고가용성 및 병렬 쿼리를 준비 하 고 실행 합니다.

EngineV3는 분산 쿼리의 "하위 파트" 최적화에 중점을 둔 것입니다.

## <a name="performance"></a>성능

향상 된 성능 및 향상 된 쿼리 속도는 다음 두 가지 주요 엔진 변경 내용에서 제공 됩니다.

* **새롭게 향상 된 분할 된 분할 저장소 형식입니다.** EngineV2와 마찬가지로 저장소 형식은 구조화 되지 않은 (텍스트) 및 반 구조화 된 데이터 형식에 특별히 주목 하는 압축 된 열 저장소입니다. EngineV3는 이러한 다양 한 데이터 형식의 인코딩을 향상 시킵니다. 인덱스는 데이터를 검색 하지 않고 인덱스를 기반으로 쿼리 부분을 평가할 수 있도록 다시 디자인 되었습니다.
* **하위 수준 분할 된 쿼리 엔진을 다시 디자인 합니다.** 새 분할 된 데이터베이스가 매우 효율적인 기계어 코드로 컴파일되어 신속 하 고 효율적인 퓨즈 쿼리 계산 논리가 생성 됩니다. 쿼리 컴파일은 모든 분할에서 수집 된 데이터 통계를 기반으로 하며 열 인코딩의 세부 사항에 맞게 조정 됩니다.

EngineV3의 성능 영향은 사용 되는 데이터 집합, 쿼리 패턴, 동시성 및 VM Sku에 따라 다릅니다. 성능 테스트에서 100-TB 데이터 집합을 사용 하 고 구조적, 비구조적, 반 구조화 된 데이터에 대 한 분석과 관련 된 다양 한 시나리오를 탐색 했습니다. 동일한 수준의 동시성 및 동일한 하드웨어 구성을 사용 하는 경우 성능 향상은 평균적으로 ~ 8X입니다. 실제 성능 향상은 쿼리 및 데이터 집합에 따라 달라 집니다.

## <a name="create-an-enginev3-cluster"></a>EngineV3 클러스터 만들기

EngineV3를 사용 하 여 [새 클러스터를 만들려면](create-cluster-database-portal.md) 클러스터 만들기 화면의 **기본 사항** 탭에서 **엔진 V3 미리 보기 사용** 확인란을 선택 합니다.

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="클러스터를 만드는 동안 엔진 V3 미리 보기 사용 확인란의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기를 사용 하 여 데이터 수집](ingest-data-overview.md)
