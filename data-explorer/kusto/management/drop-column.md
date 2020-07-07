---
title: drop column-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 drop 열에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966876"
---
# <a name="drop-column"></a>.drop column

테이블에서 열을 제거 합니다.
테이블에서 여러 열을 삭제 하려면 [아래](#drop-table-columns)를 참조 하십시오.

> [!WARNING]
> 이 명령은 취소할 수 없습니다. 제거 된 열의 모든 데이터는 삭제 됩니다.
> 해당 열을 다시 추가 하는 이후 명령은 데이터를 복원할 수 없습니다.

**구문**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>테이블 열 삭제

테이블에서 여러 열을 제거 합니다.

> [!WARNING]
> 이 명령은 취소할 수 없습니다. 제거 된 열에 있는 모든 데이터는 삭제 됩니다.
> 이러한 열을 다시 추가 하는 이후 명령은 데이터를 복원할 수 없습니다.

**구문**

`.drop``table` *TableName* `columns` TableName `(` *Col1* [ `,` *Col2*] ...`)`