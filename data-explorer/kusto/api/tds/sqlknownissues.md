---
title: SQL Server와의 kusto MS-TDS/T-sql 차이점-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto Microsoft SQL Server의 MS-TDS/T-sql 차이점에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382372"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>Kusto Microsoft SQL Server의 MS-TDS/T-sql 차이점

다음은 Kusto와 SQL Server의 T-sql 구현 간의 주요 차이점에 대 한 일부 목록입니다.

## <a name="create-insert-drop-alter-statements"></a>CREATE, INSERT, DROP, ALTER 문

Kusto는 LTDS를 통한 스키마 수정 또는 데이터 수정을 지원 하지 않으며 위의 T-sql 문도 지원 하지 않습니다.

## <a name="correlated-sub-queries"></a>상관 관계가 지정 된 하위 쿼리

Kusto `SELECT` 는, 및 절에서 상관 관계가 지정 된 하위 쿼리를 지원 하지 않습니다 `WHERE` `JOIN` .

## <a name="top-flavors"></a>상위 특색

Kusto `WITH TIES` 는 쿼리를 무시 하 고 정기적으로 평가 `TOP` 합니다.
Kusto는를 지원 하지 않습니다 `PERCENT` .

## <a name="cursors"></a>커서

Kusto는 SQL 커서를 지원 하지 않습니다.

## <a name="flow-control"></a>흐름 제어

Kusto `IF` `THEN` `ELSE` 는 `THEN` 및 일괄 처리에 대해 동일한 스키마를 포함 하는 절과 같은 몇 가지 제한 된 사례를 제외 하 고 흐름 제어 문을 지원 하지 않습니다 `ELSE` .

## <a name="data-types"></a>데이터 형식

쿼리에 따라 반환 되는 데이터의 형식이 SQL Server와 다를 수 있습니다.
이 `TINYINT` 에 대 한 간단한 예제는 `SMALLINT` Kusto에 해당 하는이 없는 및와 같은 형식입니다. 따라서 또는 형식 값을 필요로 하는 클라이언트 `BYTE` 는 `INT16` 또는 값을 대신 받을 수 있습니다 `INT32` `INT64` .

## <a name="column-order-in-results"></a>결과의 열 순서

문에서 고가 사용 되는 경우 `SELECT` 각 결과 집합의 열 순서는 Kusto와 SQL Server 사이에 다를 수 있습니다. 열 이름을 사용 하는 클라이언트는 이러한 경우 보다 효율적으로 작동 합니다.
문에 고가 없는 경우에는 `SELECT` 열 서 수가 유지 됩니다.

## <a name="columns-name-in-results"></a>결과의 열 이름

T-sql에서는 여러 열이 동일한 이름을 가질 수 있습니다. Kusto에는 허용 되지 않습니다.
이름이 충돌 하는 경우 Kusto에서 열 이름이 다를 수 있습니다.
그러나 적어도 하나 이상의 열에 대해 원래 이름이 유지 됩니다.

## <a name="any-all-and-exists-predicates"></a>ANY, ALL 및 EXISTS 조건자

Kusto는 조건자, 및을 지원 하지 않습니다 `ANY` `ALL` `EXISTS` .

## <a name="recursive-ctes"></a>재귀 CTE

Kusto는 재귀 공통 테이블 식을 지원 하지 않습니다.

## <a name="dynamic-sql"></a>동적 SQL

Kusto는 동적 SQL 문을 지원 하지 않습니다 (쿼리에 의해 생성 된 SQL 스크립트의 인라인 실행).

## <a name="within-group"></a>WITHIN GROUP

Kusto는 절을 지원 하지 않습니다 `WITHIN GROUP` .

## <a name="truncate-function"></a>TRUNCATE 함수

Kusto의 TRUNCATE 함수 (ODBC)는 ROUND와 유사 하 게 작동 합니다. 즉, 결과는 SQL에서 반환 되는 하위 항목 대신 가장 가까운 값이 됩니다.