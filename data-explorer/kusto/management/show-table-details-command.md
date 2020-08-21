---
title: . 테이블 세부 정보 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 세부 정보 표시에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5c6ceecba95911e57bad9204aeb5a48644db52ef
ms.sourcegitcommit: 94a52929380f0070de1ed8c2011bbb62ceb341fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88702496"
---
# <a name="show-table-details"></a>.show table details
지정 된 테이블 또는 데이터베이스의 모든 테이블을 포함 하는 집합을 반환 합니다 .이 집합은 각 테이블의 속성에 대 한 자세한 요약을 포함 합니다.

[데이터베이스 뷰어 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**출력**

| 출력 매개 변수           | 유형     | Description                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | 테이블의 이름입니다.                                                                          |
| `DatabaseName`             | String   | 테이블이 속한 데이터베이스입니다.                                                         |
| `Folder`                   | String   | 테이블의 폴더입니다.                                                                             |
| `DocString`                | String   | 테이블을 문서화 하는 문자열입니다.                                                                 |
| `TotalExtents`             | Int64    | 테이블의 총 익스텐트 수입니다.                                                       |
| `TotalExtentSize`          | Double   | 테이블에서 익스텐트 (압축 크기 + 인덱스 크기)의 총 크기 (바이트)입니다.               |
| `TotalOriginalSize`        | Double   | 테이블에 있는 데이터의 총 원래 크기 (바이트)입니다.                                        |
| `TotalRowCount`            | Int64    | 테이블의 총 행 수입니다.                                                          |
| `HotExtents`               | Int64    | 핫 캐시에 저장 된 테이블의 총 익스텐트 수입니다.                              |
| `HotExtentSize`            | Double   | 핫 캐시에 저장 된 익스텐트 (압축 크기 + 인덱스 크기)의 총 크기 (바이트)입니다. |
| `HotOriginalSize`          | Double   | 핫 캐시에 저장 된 테이블에 있는 데이터의 총 원래 크기 (바이트)입니다.               |
| `HotRowCount`              | Int64    | 핫 캐시에 저장 된 테이블의 총 행 수입니다.                                 |
| `AuthorizedPrincipals`     | String   | JSON으로 직렬화 된 테이블의 인증 된 보안 주체입니다.                                          |
| `RetentionPolicy`          | String   | JSON으로 직렬화 된 테이블의 유효 `*` 보존 정책입니다.                                  |
| `CachingPolicy`            | String   | JSON으로 직렬화 된 테이블의 유효 `*` 캐싱 정책입니다.                                    |
| `ShardingPolicy`           | String   | 66666666666666로 serialize 된 테이블의 유효 `*` 분할 정책입니다.                     |
| `MergePolicy`              | String   | JSON으로 serialize 된 테이블의 유효한 `*` 병합 정책입니다.                                      |
| `StreamingIngestionPolicy` | String   | JSON으로 직렬화 된 테이블의 유효 `*` 스트리밍 수집 정책입니다.                        |
| `MinExtentsCreationTime`   | DateTime | 테이블에서 익스텐트의 최소 생성 시간입니다 (익스텐트가 없는 경우 null).         |
| `MaxExtentsCreationTime`   | DateTime | 테이블에서 익스텐트의 최대 생성 시간입니다 (익스텐트가 없는 경우 null).         |
| `RowOrderPolicy`           | String   | JSON으로 직렬화 된 테이블의 유효 행 순서 정책입니다.                                     |

`*`*부모 엔터티의 정책 (예: 데이터베이스/클러스터)* 을 고려 하 고 있습니다.

**출력 예제**

| TableName         | DatabaseName | 폴더 | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | HotExtents | HotExtentSize | HotOriginalSize | HotRowCount | AuthorizedPrincipals                                                                                                                                                                               | RetentionPolicy                                                                                                                                       | CachingPolicy                                                                        | ShardingPolicy                                                                    | MergePolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCreationTime      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| 작업        | 작업   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{"Type": "AAD User", "DisplayName": "My Name (upn: alias@fabrikam.com )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "note": ""}] | {"소프트 Deleteperiod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0}  | {"DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": []} | {"MaxRowCount": 75만, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048} | {"RowCountUpperBoundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "Allowrebuild": true} | null                     |
| ServiceOperations | 작업   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{"Type": "AAD User", "DisplayName": "My Name (upn: alias@fabrikam.com )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "note": ""}] | {"소프트 Deleteperiod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0} | {"DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": []} | {"MaxRowCount": 75만, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048} | {"RowCountUpperBoundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "Allowrebuild": true} | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |
