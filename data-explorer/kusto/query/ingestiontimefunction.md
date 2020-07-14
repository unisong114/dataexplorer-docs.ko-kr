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
ms.openlocfilehash: fea923b0d917beb505bd6a1cb9ee1339739d08c6
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291562"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

현재 레코드가 수집 대략적인 시간을 반환 합니다.

이 함수는 데이터를 수집 때 [IngestionTime 정책을](../management/ingestiontimepolicy.md) 사용 하는 수집 데이터 테이블의 컨텍스트에서 사용 해야 합니다. 그렇지 않으면이 함수는 null 값을 생성 합니다.

::: zone-end

::: zone pivot="azuremonitor"

`datetime`레코드가 수집 되 고 쿼리 준비가 되었을 때를 검색 합니다.

::: zone-end

> [!NOTE]
> 수집 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있으며 여러 수집 작업이 동시에 수행 될 수 있으므로이 함수에서 반환 되는 값은 근사값입니다. 정확히 한 번만 보장 되는 테이블의 모든 레코드를 처리 하려면 [데이터베이스 커서](../management/databasecursor.md)를 사용 합니다.

**구문**

`ingestion_time()`

**반환**

`datetime`테이블에 수집 하는 대략적인 시간을 지정 하는 값입니다.

**예제**

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
