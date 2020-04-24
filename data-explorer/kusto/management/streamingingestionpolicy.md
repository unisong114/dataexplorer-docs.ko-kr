---
title: 스트리밍 수집 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 스트리밍 수집 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: ef4199e0cd8c1e14839e5508a9c6a0d793de0cc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519604"
---
# <a name="streaming-ingestion-policy"></a>스트리밍 수집 정책

## <a name="streaming-ingestion-target-scenario"></a>스트리밍 인고 대상 시나리오

스트리밍 수집은 다양한 볼륨 데이터의 경우 수집 시간이 10초 미만인 짧은 대기 시간이 필요한 시나리오를 대상으로 합니다. 하나 이상의 데이터베이스에서 각 테이블의 데이터 스트림이 상대적으로 작지만 전체 데이터 수집 볼륨(초당 수천 개)이 많은 테이블의 운영 처리를 최적화하는 데 사용됩니다.

데이터 양이 테이블당 초당 1MB 이상으로 증가하는 경우 스트리밍 수집 대신 클래식(대량) 수집을 사용합니다. 

* 이 기능을 구현하는 방법에 대해 알아보려면 [스트리밍 섭취](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)를 참조하십시오.
* 스트리밍 수집 제어 명령에 대한 자세한 내용은 [제어 명령을 사용하여 스트리밍 수집 정책을 관리하는 데 사용됩니다.](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>스트리밍 인식 정책 정의

스트리밍 통합 정책은 테이블이나 데이터베이스에서 정의할 수 있습니다. 데이터베이스 수준에서 이 정책을 정의하면 데이터베이스의 모든 기존 테이블과 이후 테이블에 동일한 설정이 적용됩니다. 스트리밍 보급 정책이 테이블 및 데이터베이스 수준 모두에서 설정된 경우 테이블 수준 설정이 우선합니다.

> [!NOTE]
> 테이블이 직접 스트리밍 을 받지 않고 업데이트 정책을 통해서만 스트리밍 을 사용할 수 없는 경우 이 테이블에 스트리밍 통합 정책을 정의할 필요가 없습니다. 

스트리밍 수집 정책은 테이블의 스트리밍 데이터가 배포될 행 저장소의 최대 수를 설정합니다. 배포는 가용성 및 데이터 속도 지원에 모두 필요합니다.

## <a name="setting-the-number-of-row-stores"></a>행 저장소 수 설정

스트리밍 통합 정책에 설정된 행 저장소 수를 정의해야 합니다. 이 숫자는 테이블당 스트리밍 데이터 속도를 기반으로 해야 합니다(대략적인 추정이 충분합니다).
모든 테이블에 대해 권장되는 행 저장소의 최소 권장 수는 4개입니다. 지원되는 행 저장소의 최대 지원 수는 64개입니다.
테이블의 스트리밍 데이터 비율이 높을수록 관련 스트리밍 수집 정책에 필요한 행 저장소 수가 높아지습니다.
권장 설정에 다음 표를 사용하십시오(의심스러운 경우 더 높은 숫자를 사용하십시오).

|예상 시간당 스트리밍 데이터 속도(테이블당)|행 저장소 수|
|----------|------|
|< 1Gb/시간 |4|
|1 - 2 GB / 시간 |4-8|
|2 - 3 GB / 시간 |8-12|
|3 - 4 GB / 시간 |12-16|
| > 4 GB / 시간 |

 이에 대한 자세한 내용은 [지원 티켓을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 엽니다.

최적의 쿼리 대기 시간의 경우 테이블당 정의된 행 저장소 수가 위의 권장 사항을 크게 초과해서는 안 됩니다.

> [!NOTE]
> 데이터베이스에 대한 스트리밍 수집 정책을 설정할 때 가장 높은 데이터 비율로 테이블에 필요한 행 저장소 수를 할당합니다. 