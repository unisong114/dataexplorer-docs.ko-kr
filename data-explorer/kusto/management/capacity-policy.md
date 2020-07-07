---
title: 용량 정책 제어 명령-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 용량 정책 제어 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967335"
---
# <a name="capacity-policy-commands"></a>용량 정책 명령

다음 제어 명령은 클러스터의 [용량 정책을](../management/capacitypolicy.md)관리 하는 데 사용할 수 있습니다.

명령에는 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요 합니다.

## <a name="show-cluster-policy-capacity"></a>클러스터 정책 용량 표시

```kusto
.show cluster policy capacity
```

클러스터에 대 한 현재 용량 정책을 표시 합니다.

**출력**

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 형식
|---|---|---|---|---
|CapacityPolicy | | 정책을 나타내는 JSON 형식 문자열입니다. | 클러스터의 데이터베이스 목록 |클러스터


## <a name="alter-cluster-policy-capacity"></a>클러스터 정책 용량 변경

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**참고**: 클러스터 용량 정책에 대 한 변경 내용을 적용 하는 데 최대 1 시간이 걸릴 수 있습니다.

**예:**

* 클러스터 정책의 모든 속성을 명시적으로 변경:

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* 다른 모든 속성을 그대로 유지 하는 클러스터 수준 정책의 단일 속성 변경:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
