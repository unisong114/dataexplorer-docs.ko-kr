---
title: ingestion_time ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 ingestion_time ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030041"
---
# <a name="ingestion_time"></a>ingestion_time()

레코드의 `$IngestionTime` 숨겨진 `datetime` 열을 검색 하거나 null을 반환 합니다.
`$IngestionTime` 열은 테이블의

::: zone pivot="azuredataexplorer"

[IngestionTime 정책이](../management/ingestiontimepolicy.md) 설정 되었습니다 (사용).

::: zone-end

::: zone pivot="azuremonitor"

IngestionTime 정책이 설정 되었습니다 (사용).

::: zone-end

테이블에이 정책이 정의 되어 있지 않으면 null 값이 반환 됩니다.

이 함수는 관련 데이터를 반환 하기 위해 실제 테이블의 컨텍스트에서 사용 해야 합니다. 예를 들어 `summarize` 연산자 다음에 호출 되는 경우 모든 레코드에 대해 null을 반환 합니다.

**구문**

 `ingestion_time()`

**반환**

테이블 `datetime` 에 수집 하는 대략적인 시간을 지정 하는 값입니다.

**예제**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
