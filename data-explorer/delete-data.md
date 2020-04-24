---
title: Azure 데이터 탐색기에서 데이터 삭제
description: 이 문서에서는 제거, 삭제 범위 및 보존 기반 삭제를 포함하여 Azure Data Explorer의 삭제 시나리오에 대해 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: efbea2f9b8502f2521f2376f4aaf57902fa4e302
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498462"
---
# <a name="delete-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 데이터 삭제

Azure Data Explorer는 이 문서에서 설명하는 다양한 삭제 시나리오를 지원합니다. 

## <a name="delete-data-using-the-retention-policy"></a>보존 정책을 사용하여 데이터 삭제

Azure Data 탐색기는 [보존 정책에](kusto/management/retentionpolicy.md)따라 데이터를 자동으로 삭제합니다. 이 방법은 데이터를 삭제하는 가장 효율적이고 번거로움없는 방법입니다. 데이터베이스 또는 테이블 수준에서 보존 정책을 설정합니다.

보존 기간이 90일로 설정된 데이터베이스 또는 테이블을 고려하세요. 60일 간의 데이터만 필요한 경우 다음과 같이 이전 데이터를 삭제합니다.

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>익스텐트 삭제하여 데이터 삭제

[익스텐트(데이터 샤드)는](kusto/management/extents-overview.md) 데이터가 저장되는 내부 구조입니다. 각 익스텐은 최대 수백만 개의 레코드를 보유할 수 있습니다. 익스텐트는 개별적으로 또는 [드롭 익스텐트 명령을](kusto/management/extents-commands.md#drop-extents)사용하여 그룹으로 삭제할 수 있습니다. 

### <a name="examples"></a>예

테이블또는 특정 익스텐트로의 모든 행을 삭제할 수 있습니다.

* 테이블의 모든 행 삭제:

    ```kusto
    .drop extents from TestTable
    ```

* 특정 익스텐스 삭제:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>지우기 사용 으로 개별 행 삭제

[데이터 제거는](kusto/concepts/data-purge.md) 개인 행을 삭제하는 데 사용할 수 있습니다. 삭제는 즉각적인 것이 아니며 상당한 시스템 리소스가 필요합니다. 따라서 규정 준수 시나리오에 대해서만 권장됩니다.  

