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
ms.openlocfilehash: a2c3580ee265e0c67003d67c7e5a68cdd9165191
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342674"
---
# <a name="external-tables"></a>외부 테이블

**외부 테이블** 은 Azure 데이터 탐색기 데이터베이스 외부에 저장 된 데이터를 참조 하는 Kusto 스키마 엔터티입니다.

[테이블과](tables.md)마찬가지로 외부 테이블에는 잘 정의 된 스키마 (열 이름 및 데이터 형식 쌍의 정렬 된 목록)가 있습니다. 테이블과 달리 데이터는 클러스터 외부에 저장 되 고 관리 됩니다. 가장 일반적으로 데이터는 CSV, Parquet, Avro와 같은 일부 표준 형식으로 저장 되며 Azure 데이터 탐색기 수집 되지 않습니다.

**외부 테이블** 은 한 번 생성 됩니다. 외부 테이블을 만들려면 다음 명령을 참조 하세요.
* [외부 테이블 일반 제어 명령](../../management/external-table-commands.md)
* [외부 SQL 테이블 만들기 및 변경](../../management/external-sql-tables.md)
* [Azure Storage 또는 Azure Data Lake에서 테이블 만들기 및 변경](../../management/external-tables-azurestorage-azuredatalake.md)

**외부 테이블** 은 [external_table ()](../../query/externaltablefunction.md) 함수를 사용 하 여 이름으로 참조할 수 있습니다. 

**참고**

* 외부 테이블 이름:
   * 대/소문자 구분.
   * Kusto 테이블 이름과 겹칠 수 없습니다.
   * [엔터티 이름](./entity-names.md)에 대 한 규칙을 따릅니다.
* 데이터베이스당 최대 외부 테이블 제한은 1000입니다.
* Kusto는 외부 테이블에 대 한 [내보내기](../../management/data-export/export-data-to-an-external-table.md) 및 [연속 내보내기를](../../management/data-export/continuous-data-export.md) 지원 하 고 [외부 테이블을 쿼리할](../../../data-lake-query-data.md)수 있습니다.
* 외부 테이블에는 [데이터 제거가](../../concepts/data-purge.md) 적용 되지 않습니다. 레코드는 외부 테이블에서 삭제 되지 않습니다.