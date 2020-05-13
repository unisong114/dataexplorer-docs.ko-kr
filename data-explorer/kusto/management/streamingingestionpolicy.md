---
title: 스트리밍 수집 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 스트리밍 수집 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: a0141482e3714ed710dbdc6fa00e3b8b396e77e6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373301"
---
# <a name="streaming-ingestion-policy"></a>스트리밍 수집 정책

## <a name="streaming-ingestion-target-scenario"></a>스트리밍 수집 대상 시나리오

스트리밍 수집은 가변 볼륨 데이터의 수집 시간이 10 초 미만인 낮은 대기 시간이 필요한 시나리오를 대상으로 합니다. 하나 이상의 데이터베이스에서 여러 테이블의 작업 처리를 최적화 하는 데 사용 됩니다. 하나 이상의 데이터베이스에서 각 테이블에 대 한 데이터 스트림이 상대적으로 작으며 (초당 몇 개의 레코드) 전체 데이터 수집 볼륨이 많은 경우 (초당 수천 개의 레코드)

데이터 양이 테이블당 초당 1mb 이상 증가 하는 경우 스트리밍 수집 대신 클래식 (bulk) 수집을 사용 합니다. 

* 이 기능을 구현 하는 방법을 알아보려면 [스트리밍](../../ingest-data-streaming.md)수집을 참조 하세요.
* 스트리밍 수집 제어 명령에 대 한 자세한 내용은 [제어 명령을 사용 하 여 스트리밍 수집 정책 관리](../management/streamingingestion-policy.md) 를 참조 하세요.

## <a name="streaming-ingestion-policy-definition"></a>수집 정책 정의 스트리밍

스트리밍 수집 정책은 테이블이 나 데이터베이스에서 정의할 수 있습니다. 데이터베이스 수준에서이 정책을 정의 하면 데이터베이스의 모든 기존 및 미래의 테이블에 동일한 설정이 적용 됩니다. 테이블 및 데이터베이스 수준 모두에서 스트리밍 수집 정책을 설정 하면 테이블 수준 설정이 우선적으로 적용 됩니다.

> [!NOTE]
> 테이블에서 스트리밍 수집을 직접 가져오지는 않지만 업데이트 정책을 통해서만이 테이블에 스트리밍 수집 정책을 정의할 필요가 없습니다. 

스트리밍 수집 정책은 테이블의 스트리밍 데이터가 배포 되는 최대 행 저장소 수를 설정 합니다. 가용성 및 데이터 요금 지원에는 배포가 필요 합니다.

## <a name="setting-the-number-of-row-stores"></a>행 저장소 수 설정

스트리밍 수집 정책에 설정 된 행 저장소 수를 정의 해야 합니다. 이 수는 테이블당 스트리밍 데이터 전송률을 기반으로 해야 합니다 (대략적인 예측 만으로도 충분).
테이블에 대해 권장 되는 최소 행 저장소 수는 4입니다. 지원 되는 최대 행 저장소 수는 64입니다.
테이블에 대 한 스트리밍 데이터 속도가 높을수록 연결 된 스트리밍 수집 정책에 필요한 행 저장소의 수가 높아집니다.
권장 설정에 대해 다음 표를 사용 합니다 (더 높은 숫자를 사용 하는 경우).

|예상 되는 최대 시간별 스트리밍 데이터 요금 (테이블당)|행 저장소 수|
|----------|------|
|1 g b/시간 < |4|
|1-2 g b/시간 |4-8|
|2-3 g b/시간 |8-12|
|3-4 g b/시간 |12-16|
| > 4 g b/시간 |

 이에 대 한 추가 조언을 [받으려면 지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 을 엽니다.

최적의 쿼리 대기 시간을 위해 테이블당 정의 된 행 저장소 수는 위의 권장 사항을 크게 초과 해서는 안 됩니다.

> [!NOTE]
> 데이터베이스에 대 한 스트리밍 수집 정책을 설정 하는 경우 데이터 전송률이 가장 높은 테이블에 필요한 행 저장소 수를 할당 합니다. 