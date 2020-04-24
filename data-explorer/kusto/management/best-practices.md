---
title: 스키마 디자인모범 사례 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 스키마 디자인에 대한 모범 사례에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f9e2d4a2ad50b140d041179736b48a41a424f5c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522171"
---
# <a name="best-practices-for-schema-design"></a>스키마 디자인에 대한 모범 사례

관리 명령의 성능이 향상되고 서비스에 더 가벼운 영향을 미치기 위해 따라야 할 몇 가지 "해야 할 일과 하지 말"이 있습니다.

## <a name="do"></a>해야 할 일

1. 여러 테이블을 만들어야 하는 경우 [`.create tables`](create-tables-command.md) 많은 `.create table` 명령을 실행하는 대신 명령을 사용합니다.
2. 여러 테이블의 이름을 바쳐야 하는 경우 각 [`.rename tables`](rename-table-command.md)테이블 쌍에 대해 별도의 호출을 발행하는 대신 에 대한 단일 호출로 이 작업을 수행합니다.
3. 파이프()에 `.show` `|`필터를 적용하는 대신 가장 낮은 범위의 명령을 사용합니다. 다음은 그 예입니다.
    - `.show cluster extents | where TableName == 'T'` 대신 `.show table T extents` 사용
    - `.show database DB schema` 대신 `.show schema | where DatabaseName == 'DB'`를 사용합니다.
4. 단일 `.show table T` 테이블에서 실제 통계를 얻어야 하는 경우에만 사용합니다. 테이블의 존재를 확인하거나 테이블의 스키마를 얻으려면 을 사용합니다. `.show table T schema as json`
5. datetime 값을 포함하는 테이블의 스키마를 정의할 때 이러한 열이 `datetime` 형식과 함께 입력되는지 확인합니다.
    - Kusto는 열 필터링에 `datetime` 최적화되어 있습니다. 열이 필터링 `string` 시간 전이나 사용 `long`중에 수행할 `datetime` 수 있는 경우 필터링을 위한 쿼리 시간으로 변환하거나 숫자(예:) 열을 변환하지 마십시오.

## <a name="dont"></a>하지 않아야 할 일

1. 명령을 너무 `.show` 자주 실행하지 마십시오(예: `.show schema` `.show databases`, `.show tables` 가능하면 반환되는 정보를 캐시합니다.
2. 큰 스키마(예: 데이터베이스가 100개 이상)인 클러스터에서 명령을 실행하지 `.show schema` 마십시오. 대신 을 [`.show databases schema`](../management/show-schema-database.md)사용합니다.
3. 명령 다음 [쿼리](index.md#combining-queries-and-control-commands) 작업을 너무 자주 실행하지 마십시오.
    - *명령 - 다음 쿼리*: 제어 명령의 결과 집합을 파이프하고 필터 / 집계를 적용하는 것을 의미합니다.
        - 예: `.show ... | where ... | summarize ...`
    - 다음과 같은 작업을 `.show cluster extents | count` 실행할 때 `| count`Kusto는 먼저 클러스터의 모든 익스텐트에 대한 모든 세부 정보를 포함하는 데이터 테이블을 준비한 다음 해당 인메모리 전용 테이블을 Kusto 엔진으로 전송하여 카운트를 수행합니다. 이것은 Kusto가 실제로 최적화되지 않은 경로에서 매우 열심히 작동하여 그러한 사소한 대답을 다시 제공합니다.
4. 데이터 수집의 일부로 익스텐트 태그를 과도하게 사용합니다. 특히 태그를 `drop-by:` 사용하는 경우 백그라운드에서 성능 지향 그루밍 프로세스를 수행하는 시스템의 기능을 제한합니다.
    - [성능 노트는 여기를](../management/extents-overview.md#extent-tagging)참조하십시오.
    