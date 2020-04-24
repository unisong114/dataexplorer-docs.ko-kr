---
title: .show 테이블 세부 정보 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .show 테이블 세부 정보를 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 6197e173df417acb1f5dc506337773f9534564bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519791"
---
# <a name="show-table-details"></a>테이블 세부 정보 표시
각 테이블의 속성에 대한 자세한 요약을 사용하여 지정된 테이블 또는 데이터베이스의 모든 테이블을 포함하는 집합을 반환합니다.

[데이터베이스 뷰어 권한이](../management/access-control/role-based-authorization.md)필요합니다.

```
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**출력**

| 출력 매개 변수           | Type     | Description                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | 테이블의 이름입니다.                                                                          |
| `DatabaseName`             | String   | 테이블이 속한 데이터베이스입니다.                                                         |
| `Folder`                   | String   | 테이블의 폴더입니다.                                                                             |
| `DocString`                | String   | 테이블을 문서화하는 문자열입니다.                                                                 |
| `TotalExtents`             | Int64    | 테이블의 총 익스텐트 수입니다.                                                       |
| `TotalExtentSize`          | Double   | 표의 총 익스텐트 크기(압축된 크기 + 인덱스 크기)입니다.                          |
| `TotalOriginalSize`        | Double   | 테이블의 총 원래 데이터 크기입니다.                                                   |
| `TotalRowCount`            | Int64    | 테이블의 총 행 수입니다.                                                          |
| `HotExtents`               | Int64    | 핫 캐시에 저장된 테이블의 총 익스텐트 수입니다.                              |
| `HotExtentSize`            | Double   | 핫 캐시에 저장된 테이블의 익스텐트 의 총 크기(압축된 크기 + 인덱스 크기)입니다. |
| `HotOriginalSize`          | Double   | 핫 캐시에 저장된 테이블의 총 원래 데이터 크기입니다.                          |
| `HotRowCount`              | Int64    | 핫 캐시에 저장된 테이블의 총 행 수입니다.                                 |
| `AuthorizedPrincipals`     | String   | JSON으로 직렬화된 테이블의 권한 부여된 보안 주체입니다.                                          |
| `RetentionPolicy`          | String   | JSON으로 직렬화된 테이블의 효과적인`*` 보존 정책입니다.                                  |
| `CachingPolicy`            | String   | JSON으로 직렬화된 테이블의 효과적인`*` 캐싱 정책입니다.                                    |
| `ShardingPolicy`           | String   | JSON.66666666666666666666으로 직렬화된 테이블의 효과적인`*` 샤딩 정책                     |
| `MergePolicy`              | String   | JSON으로 직렬화된 테이블의 효과적인`*` 병합 정책입니다.                                      |
| `StreamingIngestionPolicy` | String   | JSON으로 직렬화된 테이블의 효과적인`*` 스트리밍 사용 정책입니다.                        |
| `MinExtentsCreationTime`   | DateTime | 익스텐트의 최소 생성 시간(또는 익스가 없는 경우 null)입니다.         |
| `MaxExtentsCreationTime`   | DateTime | 익스텐트의 최대 생성 시간(또는 익스가 없는 경우 null)입니다.         |
| `RowOrderPolicy`           | String   | JSON으로 직렬화된 테이블의 유효 행 순서 정책입니다.                                     |

`*`*상위 엔터티(예: 데이터베이스/클러스터)의 정책을 고려합니다.*

**출력 예제**

| TableName         | DatabaseName | 폴더 | 닥스트링 (것)과 함께 | 총 범위 | 총익 크기 | 토탈 오리지널 사이즈 | 총행수 | 핫 익스텐트 | 핫익스텐트사이즈 | 핫 오리지널사이즈 | 핫로우카운트 | 공인 교장                                                                                                                                                                               | 보존 정책                                                                                                                                       | 캐싱 정책                                                                        | 샤딩 정책                                                                    | 병합 정책                                                                                                                                             | 스트리밍정책 | 최소익 생성 시간      | 맥스익스텐트 생성 시간      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| 작업        | 작업   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{"유형": "AAD 사용자", "디스플레이 이름": "내 alias@fabrikam.com이름(upn: )", "ObjectId": "a7a777777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser=a7a777777-4c21-4649-95c5-350bf486087b", "참고 사항" | {"SoftDeletePeriod": "365.00:00:00:00", "컨테이너 재활용 기간": "1.00:00:00:00", "익스텐트데이터크기제한바이트": 0, "OriginalDataSizeLimitInBytes": 0 }  | { "DataHotSpan": "4.00:00:00:00", "인덱스핫스팬": "4.00:00:00", "컬럼오버라이드": [] | { "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 } | { "RowCountUpperboundForMerge": 0, "MaxExtentsToMerge": 100, "루프 기간": "01:00:00", "MaxRangeInHours": 3, "허용 Rebuild": true, "허용 병합": true } | null                     |
| ServiceOperations | 작업   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{"유형": "AAD 사용자", "디스플레이 이름": "내 alias@fabrikam.com이름(upn: )", "ObjectId": "a7a777777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser=a7a777777-4c21-4649-95c5-350bf486087b", "참고 사항" | { "소프트 DeletePeriod": "365.00:00:00:00", "컨테이너 재활용 기간": "1.00:00:00:00", "익스텐트데이터크기제한바이트": 0, "OriginalDataSizeLimitInBytes": 0 } | { "DataHotSpan": "4.00:00:00:00", "인덱스핫스팬": "4.00:00:00", "컬럼오버라이드": [] | { "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 } | { "RowCountUpperboundForMerge": 0, "MaxExtentsToMerge": 100, "루프 기간": "01:00:00", "MaxRangeInHours": 3, "허용 Rebuild": true, "허용 병합": true } | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |