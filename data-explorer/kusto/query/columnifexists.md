---
title: column_ifexists ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 column_ifexists ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5eeecf9e4756ac18cdeb5c6297aea1bcca5bac14
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348856"
---
# <a name="column_ifexists"></a>column_ifexists()

는 열 이름을 문자열로 사용 하 고 기본값을 사용 합니다. 열이 있으면 해당 열에 대 한 참조를 반환 하 고, 그렇지 않으면 기본값을 반환 합니다.

## <a name="syntax"></a>구문

`column_ifexists(`*columnName* `, ` *defaultValue*)

## <a name="arguments"></a>인수

* *columnName*: 열의 이름입니다.
* *defaultValue*: 함수가 사용 된 컨텍스트에 열이 없는 경우 사용할 값입니다.
                  이 값은 모든 스칼라 식 (예: 다른 열에 대 한 참조) 일 수 있습니다.

## <a name="returns"></a>반환

*ColumnName* 이 있으면 해당 열은 참조 하는 열입니다. 그렇지 않으면- *defaultValue*입니다.

## <a name="examples"></a>예

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```