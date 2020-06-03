---
title: 명령 및 쿼리 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 명령 및 쿼리 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: c7f692739071496ce492d168c6036a2c2adac8fd
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329048"
---
# <a name="commands-and-queries-management"></a>명령 및 쿼리 관리

## <a name="show-commands-and-queries"></a>. 명령 및 쿼리 표시 

`.show``commands-and-queries`최종 상태에 도달한 관리자 명령 및 쿼리를 포함 하는 테이블을 반환 합니다. 이러한 명령 및 쿼리는 30 일 동안 사용할 수 있습니다.

명령의 출력에 표시 되는 정보는 [. show commands](commands.md) 및 [. show queries](queries.md)와 유사 하지만 기본적으로 두 결과 집합을 간단한 방법으로 조인할 수 있습니다.

**구문**

`.show` `commands-and-queries`
 
**출력**
 
출력 스키마는 다음과 같습니다.

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | 문자열     |
| CommandType              | 문자열     |
| 텍스트                     | 문자열     |
| 데이터베이스                 | 문자열     |
| StartedOn                | Datetime   |
| LastUpdatedOn            | Datetime   |
| 기간                 | timespan   |
| 주                    | 문자열     |
| FailureReason            | 문자열     |
| RootActivityId           | guid       |
| 사용자                     | 문자열     |
| 애플리케이션              | 문자열     |
| 주 서버                | 문자열     |
| ClientRequestProperties  | 동적    |
| TotalCpu                 | timespan   |
| MemoryPeak               | long       |
| CacheStatistics          | 동적    |
| ScannedExtentsStatistics | 동적    |
| ResultSetStatistics      | 동적    |

> [!NOTE]
> 쿼리의 경우 값은 `CommandType` `Query` 입니다.
