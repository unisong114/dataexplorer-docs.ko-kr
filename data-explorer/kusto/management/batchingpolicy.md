---
title: 인시션 배치 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인시션 배치 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: fa3a4cfd512e3e37ba6b6abf8f8316d6ff12fdec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522239"
---
# <a name="ingestionbatching-policy"></a>인제션배치 정책

## <a name="overview"></a>개요

수집 프로세스 중에 Kusto는 수집을 기다리는 동안 작은 데이터 청크를 함께 일괄 처리하여 처리량을 최적화하려고 시도합니다.
이러한 종류의 일괄 처리는 수집 프로세스에서 소비되는 리소스를 줄여줄뿐만 아니라 비일괄 수집에서 생성된 작은 데이터 샤드를 최적화하기 위해 수집 후 리소스가 필요하지 않습니다.

그러나 강제 지연이 도입된 수집 전에 일괄 처리를 수행하면 쿼리준비가 될 때까지 데이터 수집을 요청하는 종단 간 시간이 커지도록 하는 단점이 있습니다.

이 절충을 제어할 수 있도록 `IngestionBatching` 정책을 사용할 수 있습니다.
이 정책은 큐에 대기된 정리에만 적용되며 작은 Blob을 함께 일괄 처리할 때 허용하는 최대 강제 지연을 제공합니다.

## <a name="details"></a>세부 정보

위에서 설명한 것처럼 대량으로 수집할 데이터의 최적 크기가 있습니다.
현재 이 크기는 압축되지 않은 데이터의 약 1GB입니다. 최적의 크기보다 훨씬 적은 데이터를 보유하는 Blob에서 수행되는 수집은 최적이 아니므로 큐에 대기된 섭취에서 Kusto는 이러한 작은 Blob을 함께 배치합니다. 일괄 처리는 첫 번째 조건이 true가 될 때까지 수행됩니다.

1. 일괄 처리된 데이터의 총 크기가 최적의 크기에 도달하거나
2. 최대 지연 시간, 총 크기 또는 정책에서 허용하는 `IngestionBatching` Blob 수에 도달했습니다.

`IngestionBatching` 정책은 데이터베이스 또는 테이블에 설정할 수 있습니다. 기본적으로 정책이 정의되지 않은 경우 Kusto는 최대 지연 시간, **1000개** 항목, 일괄 처리를 위한 총 크기 **1G로** **기본값을 5분으로** 사용합니다.

> [!WARNING]
> 이 정책을 설정하려는 고객은 먼저 Kusto ops 팀에 문의하는 것이 좋습니다. 이 정책을 매우 작은 값으로 설정하는 영향은 클러스터의 COGS가 증가하고 성능이 저하되는 것입니다. 또한 한도에서 이 값을 줄이면 여러 개의 섭취 프로세스를 병렬로 관리하는 오버헤드로 인해 실제로 효과적인 엔드 투 엔드 섭취 대기 시간이 **증가할** 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* [인제션배치 정책 명령 참조](../management/batching-policy.md)
* [섭취 모범 사례 - 처리량 최적화](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)