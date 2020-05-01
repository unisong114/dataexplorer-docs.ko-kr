---
title: . 열 변경-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter 열에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617870"
---
# <a name="alter-column"></a>.alter column

기존 테이블 열의 데이터 형식을 변경 합니다.

> [!WARNING]
> 열의 데이터 형식을 변경 하는 경우 새 데이터 형식이 아닌 해당 열의 기존 데이터는 이후 쿼리에서 무시 되 고 [null 값](../query/scalar-data-types/null-values.md)으로 대체 됩니다. 를 사용한 `alter column`후에는 다른 명령을 사용 하 여 열 형식을 다시 이전 값으로 변경 하는 경우에도 해당 데이터를 복구할 수 없습니다.
> 데이터 손실 없이 열 유형을 변경 하는 방법에 대 한 권장 절차는 [아래](#changing-column-type-without-data-loss) 를 참조 하세요.

**구문** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* ColumnName `type` *ColumnNewType* ColumnNewType `=`
 
**예제** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>데이터 손실 없이 열 형식 변경

기록 데이터를 유지 하면서 열 유형을 변경 하려면 올바른 형식의 새 테이블을 만듭니다.

에서 열 형식을 `T1` 변경 하려는 각 테이블에 대해 다음 단계를 실행 합니다.

1. 올바른 스키마 ( `T1_prime` 올바른 열 유형)를 사용 하 여 테이블을 만듭니다.
1. 테이블 이름 바꾸기를 사용 하 여 테이블 이름 [바꾸기](rename-table-command.md) 명령을 사용 하 여 테이블을 교환 합니다.

```kusto
.rename tables T_prime=T1, T1=T_prime
```

명령이 완료 되 면 이제에 `T1` 새 데이터 흐름이 올바르게 입력 되 고 기록 데이터를에서 `T1_prime`사용할 수 있습니다.

데이터가 `T1_prime` 보존 기간을 벗어날 때까지 union을 수행 하도록 `T1` 쿼리를 변경 해야 `T1_prime`합니다.