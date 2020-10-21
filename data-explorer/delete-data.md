---
title: Azure 데이터 탐색기에서 데이터 삭제
description: 이 문서에서는 제거, 범위 삭제 및 보존 기반 삭제를 포함 하 여 Azure 데이터 탐색기의 삭제 시나리오에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 962735c666fa94a0b86cfae8775f712ee42e6d33
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342844"
---
# <a name="delete-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 데이터 삭제

Azure 데이터 탐색기는이 문서에 설명 된 다양 한 삭제 시나리오를 지원 합니다. 

## <a name="delete-data-using-the-retention-policy"></a>보존 정책을 사용 하 여 데이터 삭제

Azure 데이터 탐색기는 [보존 정책](kusto/management/retentionpolicy.md)에 따라 데이터를 자동으로 삭제 합니다. 이 방법은 데이터를 삭제 하는 가장 효율적인 방법입니다. 데이터베이스 또는 테이블 수준에서 보존 정책을 설정 합니다.

보존 기간이 90일로 설정된 데이터베이스 또는 테이블을 고려하세요. 60 일간의 데이터만 필요한 경우 다음과 같이 오래 된 데이터를 삭제 합니다.

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>익스텐트를 삭제 하 여 데이터 삭제

[익스텐트 (데이터 분할)](kusto/management/extents-overview.md) 는 데이터가 저장 되는 내부 구조입니다. 각 익스텐트는 수백만 개의 레코드를 보유할 수 있습니다. 익스텐트는 [drop 익스텐트의 명령을](./kusto/management/drop-extents.md)사용 하 여 개별적으로 또는 그룹으로 삭제할 수 있습니다.

### <a name="examples"></a>예

테이블의 모든 행 또는 특정 익스텐트를 삭제할 수 있습니다.

* 테이블의 모든 행을 삭제 합니다.

    ```kusto
    .drop extents from TestTable
    ```

* 특정 익스텐트를 삭제 합니다.

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>제거를 사용 하 여 개별 행 삭제

[데이터 제거](kusto/concepts/data-purge.md) 를 사용 하 여 개별 행을 삭제할 수 있습니다. 삭제는 즉각적이 아니며 상당한 시스템 리소스가 필요 합니다. 따라서 규정 준수 시나리오에만 권장 됩니다.