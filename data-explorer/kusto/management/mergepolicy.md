---
title: 익스텐트 병합 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 병합 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: cfb75520a5fa173757395628948c8630a258f935
ms.sourcegitcommit: 4e46b497d518884693a142f4ae21ea497db81861
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83824903"
---
# <a name="extents-merge-policy"></a>익스텐트 병합 정책
병합 정책은 Kusto 클러스터의 [익스텐트 (데이터 분할)](../management/extents-overview.md) 를 병합할지 여부 및 방법을 정의 합니다.

Merge 작업에는 `Merge` (인덱스를 다시 작성 하는) 및 `Rebuild` (데이터를 완전히 다시 수집)의 두 가지 종류가 있습니다.

두 작업 종류는 모두 원본 익스텐트를 대체 하는 단일 익스텐트를 사용 하 여 결과를 생성 합니다.

다시 빌드 작업은 기본적으로 기본 설정 되어 있으며, 다시 작성 되는 조건에 맞지 않는 남은 익스텐트가 있는 경우에만 병합이 시도 됩니다.  

*참고:*
- *다른* 태그를 사용 하 여 익스텐트의 태그를 지정 하면 `drop-by` 병합 정책이 설정 된 경우에도 이러한 익스텐트가 함께 병합 되지 않습니다 ( [익스텐트 태깅](../management/extents-overview.md#extent-tagging)참조).
- 태그의 합집합을 1M 문자 길이를 초과 하는 익스텐트는 함께 병합 되지 않습니다.
- 데이터베이스의 [분할 정책은](./shardingpolicy.md) 익스텐트가 함께 병합 되는 방법에도 영향을 줍니다.

병합 정책에는 다음과 같은 속성이 포함 되어 있습니다.

- **RowCountUpperBoundForMerge**:
    - 기본값은 0입니다.
    - 병합 된 익스텐트의 허용 되는 최대 행 수입니다.
    - 다시 빌드하지 않고 병합 작업에 적용 됩니다.  
- **MaxExtentsToMerge**:
    - 기본값은 100입니다.
    - 단일 작업에서 병합할 수 있는 최대 익스텐트 수입니다.
    - 병합 작업에 적용 됩니다.
- **LoopPeriod**:
    - 기본값은 01:00:00 (1 시간)입니다.
    - 데이터 관리 서비스에서 수행 하는 병합/다시 작성 작업의 연속 반복 2 회 시작 사이에 대기 하는 최대 시간입니다.
    - 병합 및 다시 작성 작업에 모두 적용 됩니다.
- **Allowrebuild**:
    - 기본값은 ' t r u e '입니다.
    - `Rebuild`작업을 사용할 수 있는지 여부를 정의 합니다 .이 경우 작업 보다 우선 `Merge` 합니다.
- **Allowmerge**:
    - 기본값은 ' t r u e '입니다.
    - 작업을 사용할지 여부를 정의 `Merge` 합니다 .이 경우 작업 보다 우선 순위가 낮습니다 `Rebuild` .
- **Maxrange Inhours**:
    - 기본값은 8입니다.
    - 두 개의 다른 익스텐트의 생성 시간 사이에 허용 되는 최대 차이 (시간) 이며 여전히 병합 될 수 있습니다.
    - 타임 스탬프는 익스텐트를 만드는 것 이며, 범위에 포함 된 실제 데이터와는 관련이 없습니다.
    - 병합 및 다시 작성 작업에 모두 적용 됩니다.
    - 모범 사례는이 값이 데이터베이스의 [보존 정책](./retentionpolicy.md)에 대 한 *소프트 Deleteperiod*또는 [캐시 정책의](./cachepolicy.md) *DataHotSpan* (둘 중 낮은 값)와 상관 2-3 관계를 지정 하는 것입니다.

**`MaxRangeInHours`예와**
|최소 (소프트 Deleteperiod (보존 정책), DataHotSpan (캐시 정책))|최대 범위 (시간) (병합 정책)
|---|---
|7 일 (168 시간)| 4
|14 일 (336 시간)| 8
|30 일 (720 시간)| 18
|60 일 (1440 시간)| 36
|90 일 (2160 시간)| 60
|180 일 (4320 시간)| 120
|365 일 (8760 시간)| 250

> [!WARNING]
> 익스텐트 병합 정책을 변경 하기 전에 Azure 데이터 탐색기 팀에 문의 하세요.

데이터베이스를 만들 때 기본 병합 정책 (위에서 언급 한 기본값이 포함 된 정책)을 사용 하 여 설정 합니다. 즉, 해당 정책이 테이블 수준에서 명시적으로 재정의 되지 않는 한 데이터베이스에 생성 된 모든 테이블에 의해 상속 됩니다.

데이터베이스/테이블에 대 한 병합 정책을 관리할 수 있는 제어 명령은 [여기](../management/merge-policy.md)에서 찾을 수 있습니다.
