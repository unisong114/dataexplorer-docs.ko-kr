---
title: 익스텐트 병합 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 병합 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 87980046e6f0ebbbdd17a9037aa1206779d2a61e
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057039"
---
# <a name="merge-policy"></a>병합 정책

병합 정책은 Kusto 클러스터의 [익스텐트 (데이터 분할)](../management/extents-overview.md) 를 병합할지 여부 및 방법을 정의 합니다.

`Merge`인덱스를 다시 작성 하는와 `Rebuild` 데이터를 완전히 reingests 하는 두 가지 유형의 병합 작업이 있습니다.

두 작업 유형 모두 원본 익스텐트를 대체 하는 단일 익스텐트를 생성 합니다.

기본적으로 다시 작성 작업을 수행 하는 것이 좋습니다. 다시 작성 되는 조건에 맞지 않는 익스텐트가 있으면 병합을 시도 하 게 됩니다.  

> [!NOTE]
> * *다른* 태그를 사용 하 여 익스텐트의 태그를 지정 하면 `drop-by` 병합 정책이 설정 된 경우에도 이러한 익스텐트가 병합 되지 않습니다. 자세한 내용은 [익스텐트 태그](../management/extents-overview.md#extent-tagging)지정을 참조 하세요.
> * 태그의 합집합은 1M 문자 길이를 초과 하는 익스텐트는 병합 되지 않습니다.
> * 데이터베이스 또는 테이블의 [분할 정책은](./shardingpolicy.md) 익스텐트가 병합 되는 방법에도 영향을 줍니다.

## <a name="merge-policy-properties"></a>병합 정책 속성

병합 정책에는 다음과 같은 속성이 포함 되어 있습니다.

* **RowCountUpperBoundForMerge**:
    * 기본값:
      * 6 월 2020 일 이전에 설정 된 정책에 대해 0 (무제한)입니다.
      * 1600만 6 월 2020 일부 터 설정 된 정책에 대 한.
    * 병합 된 익스텐트의 허용 되는 최대 행 수입니다.
    * 다시 빌드하지 않고 병합 작업에 적용 됩니다.  
* **OriginalSizeMBUpperBoundForMerge**:
    * 기본값은 0 (무제한)입니다.
    * 병합 된 익스텐트의 최대 허용 원본 크기 (Mb)입니다.
    * 다시 빌드하지 않고 병합 작업에 적용 됩니다.  
* **MaxExtentsToMerge**:
    * 기본값은 100입니다.
    * 단일 작업에서 병합할 수 있는 최대 익스텐트 수입니다.
    * 병합 작업에 적용 됩니다.
* **LoopPeriod**:
    * 기본값은 01:00:00 (1 시간)입니다.
    * 데이터 관리 서비스에서 병합 또는 다시 작성 작업을 연속 해 서 두 번 반복 하기 전까지 대기 하는 최대 시간입니다.
    * 병합 및 다시 작성 작업에 모두 적용 됩니다.
* **Allowrebuild**:
    * 기본값은 ' t r u e '입니다.
    * 작업을 사용할지 여부를 정의 `Rebuild` 합니다 .이 경우 작업 보다 우선 `Merge` 합니다.
* **Allowmerge**:
    * 기본값은 ' t r u e '입니다.
    * 작업이 사용 되는지 여부를 정의 합니다 `Merge` .이 경우 작업 보다 우선 순위가 낮습니다 `Rebuild` .
* **Maxrange Inhours**:
    * 기본값은 8입니다.
        * 구체화 된 뷰의 유효 [보존 정책](retentionpolicy.md)에서 복구를 사용 하지 않도록 설정 하지 않는 한 [구체화 된 뷰의](materialized-views/materialized-view-overview.md)기본값은 14 일입니다.
    * 다른 두 익스텐트의 생성 시간 사이에 허용 되는 최대 차이 (시간)로, 계속 병합할 수 있습니다.
    * 타임 스탬프는 범위를 만들고 범위에 포함 된 실제 데이터와는 관련이 없습니다.
    * 병합 및 다시 작성 작업에 모두 적용 됩니다.
    * 이 값은 유효한 [보존 정책](./retentionpolicy.md) *소프트 deleteperiod*또는 [캐시 정책](./cachepolicy.md) *DataHotSpan* 값에 따라 설정 해야 합니다. *소프트 Deleteperiod* 및 *DataHotSpan*의 낮은 값을 사용 합니다. *Maxbetween Inhours* 값을 2-3%의 값으로 설정 합니다. [예제](#maxrangeinhours-examples) 를 참조 하세요.

## <a name="maxrangeinhours-examples"></a>Maxrange Inhours 예

|최소 (소프트 Deleteperiod (보존 정책), DataHotSpan (캐시 정책))|최대 범위 (시간) (병합 정책)|
|--------------------------------------------------------------------|---------------------------------|
|7 일 (168 시간)                                                  | 4                               |
|14 일 (336 시간)                                                 | 8                               |
|30 일 (720 시간)                                                 | 18                              |
|60 일 (1440 시간)                                               | 36                              |
|90 일 (2160 시간)                                               | 60                              |
|180 일 (4320 시간)                                              | 120                             |
|365 일 (8760 시간)                                              | 250                             |

> [!WARNING]
> 익스텐트 병합 정책을 변경 하기 전에 Azure 데이터 탐색기 팀에 문의 하세요.

데이터베이스를 만들면 위에서 언급 한 기본 병합 정책 값으로 설정 됩니다. 정책은 테이블 수준에서 명시적으로 재정의 되지 않는 한 기본적으로 데이터베이스에 생성 된 모든 테이블에 상속 됩니다.

자세한 내용은 [데이터베이스 또는 테이블에 대 한 병합 정책을 관리 하는 데 사용할 수 있는 제어 명령](../management/merge-policy.md)을 참조 하세요.
