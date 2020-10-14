---
title: Kusto IngestionBatching 정책에서 일괄 처리 최적화-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 IngestionBatching 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 27c92b9081844df0e8f8d4dc207ba5d2a8a2e2f3
ms.sourcegitcommit: a10e7c6ba96bdb94d95ef23f5d1506eb8fda0041
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058668"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching 정책

## <a name="overview"></a>개요

수집 프로세스 중에는 수집을 기다리는 동안 작은 수신 데이터 청크를 일괄 처리 하 여 처리량을 최적화 하려고 시도 합니다.
이러한 종류의 일괄 처리는 수집 프로세스에서 사용 하는 리소스를 줄이고 일괄 처리 되지 않은 수집에 의해 생성 된 작은 데이터 분할을 최적화 하기 위해 수집 후 리소스가 필요 하지 않습니다.

그러나 수집 전에 일괄 처리를 수행 하는 것이 좋습니다 .이는 강제 지연을 도입 하 여 쿼리 준비가 될 때까지 데이터 수집을 요청 하는 것과 같은 종단 간 시간이 더 클 수 있습니다.

이러한 절충에 대 한 제어를 허용 하려면 정책을 사용할 수 있습니다 `IngestionBatching` .
이 정책은 대기 중인 수집에만 적용 되며, 작은 blob을 함께 일괄 처리할 때 허용 되는 최대 강제 지연을 제공 합니다.

## <a name="details"></a>세부 정보

위에서 설명한 것 처럼 대량으로 수집 하는 최적의 데이터 크기를 가집니다.
현재 크기는 약 1gb의 압축 되지 않은 데이터입니다. 최적의 크기 보다 훨씬 적은 데이터를 보유 하는 blob에서 수행 되는 수집은 최적이 아니므로 대기 중인 수집 Kusto는 이러한 작은 blob을 함께 배치 합니다. 일괄 처리는 첫 번째 조건이 true가 될 때까지 수행 됩니다.

1. 일괄 처리 된 데이터의 총 크기가 최적의 크기에 도달 하거나
2. 정책에서 허용 하는 최대 지연 시간, 전체 크기 또는 blob 수에 `IngestionBatching` 도달 했습니다.

`IngestionBatching`데이터베이스 또는 테이블에서 정책을 설정할 수 있습니다. 기본적으로 정책이 정의 되지 않은 경우 Kusto는 최대 지연 시간, **1000** 항목, 일괄 처리에 대 한 총 **1g** 크기의 기본값을 **5 분** 으로 사용 합니다.

> [!WARNING]
> 이 정책을 매우 작은 값으로 설정 하면 클러스터의 COGS 증가 하 고 성능이 저하 됩니다. 또한이 값을 줄이면 여러 수집 프로세스를 병렬로 관리 하는 오버 헤드로 인해 실제 종단 간 수집 대기 시간이 **늘어날** 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [IngestionBatching 정책 명령 참조](../management/batching-policy.md)
* [수집 모범 사례-처리량 최적화](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)
