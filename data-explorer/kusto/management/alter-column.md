---
title: .alter 열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .alter 열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522613"
---
# <a name="alter-column"></a>.alter 열

기존 테이블 열의 데이터 형식을 변경합니다.

> [!WARNING]
> 열의 데이터 형식을 변경할 때 새 데이터 형식이 아닌 해당 열의 기존 데이터는 이후 쿼리에서 무시되고 null [값으로](../query/scalar-data-types/null-values.md)대체됩니다. 을 `alter column`사용한 후에는 다른 명령을 사용하여 열 형식을 이전 값으로 다시 변경하더라도 해당 데이터를 복구할 수 없습니다.
> 데이터를 잃지 않고 열 유형을 변경하는 권장 절차는 [아래를](#changing-column-type-without-data-loss) 참조하십시오.

**구문** 

`.alter``column` [*데이터베이스 이름]* `.` *테이블 이름* `.` *열 열* `type` `=` *열NewType*
 
**예제** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>데이터 손실 없이 열 유형 변경

기록 데이터를 유지하면서 열 유형을 변경하려면 제대로 입력된 새 테이블을 만듭니다.

열 유형을 `T1` 변경하려는 각 테이블에 대해 다음 단계를 실행합니다.

1. 올바른 스키마(오른쪽 열 유형)가 있는 테이블을 `T1_prime` 만듭니다.
1. 테이블 이름을 바꿀 수 있는 [.rename table](rename-table-command.md) 명령을 사용하여 테이블을 바꿉니다.

```
.rename tables T_prime=T1, T1=T_prime
```

명령이 완료되면 새 데이터가 `T1` 올바르게 입력되고 `T1_prime`에서 기록 데이터를 사용할 수 있습니다.

데이터가 `T1_prime` 보존 창밖으로 `T1` 나갈 `T1_prime`때까지.