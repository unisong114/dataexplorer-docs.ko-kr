---
title: 외부 테이블-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 7f74732ed38d0b41a857fc549f549ce54ad4dce6
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863713"
---
# <a name="external-tables"></a>외부 테이블

**외부 테이블** 은 kusto 데이터베이스 외부에 저장 된 데이터를 참조 하는 kusto 스키마 엔터티입니다.

[테이블과](tables.md)마찬가지로 외부 테이블에는 잘 정의 된 스키마 (열 이름 및 데이터 형식 쌍의 정렬 된 목록)가 있습니다. 테이블과 달리 데이터는 Kusto 클러스터 외부에 저장 되 고 관리 됩니다. 가장 일반적으로 데이터는 CSV, Parquet, Avro와 같은 일부 표준 형식으로 저장 되며, Kusto로 수집 되지 않습니다.

**외부 테이블** 은 한 번 생성 됩니다 ( [외부 테이블 일반 제어 명령](../../management/externaltables.md), [create 및 alter external SQL tables](../../management/external-sql-tables.md), create [및 alter table in storage](../../management/external-tables-azurestorage-azuredatalake.md)). 그리고 [external_table ()](../../query/externaltablefunction.md) 함수를 사용 하 여 이름으로 참조할 수 있습니다. 

**참고 사항**

* 외부 테이블 이름은 대/소문자를 구분 합니다.
* 외부 테이블 이름은 Kusto 테이블 이름과 겹칠 수 없습니다.
* 외부 테이블 이름은 [엔터티 이름](./entity-names.md)에 대 한 규칙을 따릅니다.
* 데이터베이스당 최대 외부 테이블 제한은 1000입니다.
* Kusto [는 외부 테이블로 데이터를 내보내고](../../management/data-export/export-data-to-an-external-table.md) 외부 테이블을 [쿼리](../../../data-lake-query-data.md)하는 것을 지원 합니다.
