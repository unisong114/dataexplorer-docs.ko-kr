---
title: MS-TDS/T-SQL 쿠스토 마이크로소프트 SQL 서버 간의 차이 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto Microsoft SQL Server 간의 MS-TDS/T-SQL 차이점에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523429"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>쿠스토 마이크로 소프트 SQL 서버 사이의 MS-TDS / T-SQL 차이

다음은 Kusto와 SQL Server의 T-SQL 구현 간의 주요 차이점의 일부 목록입니다.

## <a name="create-insert-drop-alter-statements"></a>문 만들기, 삽입, 삭제, ALTER

Kusto는 MS-TDS를 통한 스키마 수정 또는 데이터 수정을 지원하지 않으며 위의 T-SQL 문을 지원하지도 않습니다.

## <a name="correlated-sub-queries"></a>상관 하위 쿼리

Kusto는 `SELECT`에서 `WHERE`상호 연관된 하위 쿼리를 `JOIN` 지원하지 않습니다.

## <a name="top-flavors"></a>탑 맛

Kusto는 `WITH TIES` 쿼리를 무시하고 일반 `TOP`쿼리로 평가합니다.
쿠스토는 지원하지 `PERCENT`않습니다.

## <a name="cursors"></a>커서

Kusto는 SQL 커서를 지원하지 않습니다.

## <a name="flow-control"></a>흐름 제어

Kusto는 `IF` `THEN` `ELSE` `THEN` 흐름 제어 문을 지원하지 않지만, 및 일괄 처리에 대해 동일한 스키마를 `ELSE` 가진 절과 같은 몇 가지 제한된 경우를 제외하고는 지원하지 않습니다.

## <a name="data-types"></a>데이터 형식

쿼리에 따라 반환되는 데이터는 SQL Server와 다른 형식을 가질 수 있습니다.
여기에 명백한 예는 Kusto에 해당하지 않는 유형과 `TINYINT` `SMALLINT` 같은 유형입니다. 따라서 형식의 `BYTE` 값을 기대 하거나 `INT16` 대신 `INT32` 값 `INT64` 또는 값을 얻을 수 있는 클라이언트입니다.

## <a name="column-order-in-results"></a>결과의 열 순서

명령문에서 아스테릭스를 `SELECT` 사용하는 경우 각 결과 집합의 열 순서가 Kusto와 SQL Server 간에 다를 수 있습니다. 이러한 경우 열 이름을 사용하는 클라이언트가 더 잘 작동합니다.
`SELECT` 명령문에 아스테릭스 문자가 없는 경우 열 서수는 유지됩니다.

## <a name="columns-name-in-results"></a>결과의 열 이름

T-SQL에서 여러 열의 이름이 같을 수 있습니다. 쿠스토에서는 이 것을 허용하지 않습니다.
이름에서 충돌이 발생할 경우 Kusto에서 열 이름이 다를 수 있습니다.
그러나 원래 이름은 적어도 열 중 하나에 대해 유지됩니다.

## <a name="any-all-and-exists-predicates"></a>ANY, ALL 및 EXISTS 조건자

Kusto는 술어를 지원하지 `ANY`않습니다. `ALL` `EXISTS`

## <a name="recursive-ctes"></a>재귀 CTE

Kusto는 재귀 공통 테이블 식을 지원하지 않습니다.

## <a name="dynamic-sql"></a>동적 SQL

Kusto는 동적 SQL 문을 지원하지 않습니다(쿼리에서 생성된 SQL 스크립트의 인라인 실행).

## <a name="within-group"></a>WITHIN GROUP

Kusto는 절을 `WITHIN GROUP` 지원하지 않습니다.

## <a name="truncate-function"></a>트렁킨 기능

Kusto의 TRUNCATE 함수(ODBC)는 ROUND와 유사하게 작동하므로 SQL에서 반환되는 하위 값 대신 가장 가까운 값이 됩니다.