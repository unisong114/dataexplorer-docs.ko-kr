---
title: 좁은 플러그 인-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 좁은 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e597a2467da21a2c9e83aba28a1e83b242f61c75
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346680"
---
# <a name="narrow-plugin"></a>narrow 플러그 인

```kusto
T | evaluate narrow()
```

`narrow`플러그 인은 행 번호, 열 유형 및 열 값 (as)의 세 열이 있는 테이블에 넓은 테이블을 "unpivots" `string` 합니다.

`narrow`플러그 인은 가로로 스크롤할 필요 없이 넓은 테이블이 편안 하 게 표시 될 수 있도록 주로 표시를 위해 설계 되었습니다.

## <a name="syntax"></a>Syntax

`T | evaluate narrow()`

## <a name="examples"></a>예제

다음 예제에서는 Kusto 제어 명령의 출력을 쉽게 읽는 방법을 보여 줍니다 `.show diagnostics` .

```kusto
.show diagnostics
 | evaluate narrow()
```

자체의 결과는 `.show diagnostics` 단일 행 및 33 열이 있는 테이블입니다. 플러그 인을 사용 하면 `narrow` 출력이 다음과 같이 "회전" 됩니다.

행  | 열                              | 값
-----|-------------------------------------|-----------------------------
0    | I시트                           | True
0    | IsRebalanceRequired                 | False
0    | IsScaleOutRequired                  | False
0    | MachinesTotal                       | 2
0    | MachinesOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59:18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58:41.6741934
0    | ClusterWarmDataCapacityFactor       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | DiskColdAllocationPercentage        | 5
0    | InstancesTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | IngestionsSuccessRate               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | False
0    | RebalanceLastRunOn                  | 2017-03-21 09:14:53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09:19:54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | n
0    | IsAttentionRequired                 | False
0    | Attyonereason             | n
0    | ProductVersion                      | KustoRelease_2017 03.13.2
0    | FailedIngestOperations              | 0
0    | FailedMergeOperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230