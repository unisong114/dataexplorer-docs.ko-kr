---
title: column_ifexists() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 column_ifexists()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517173"
---
# <a name="column_ifexists"></a>column_ifexists()

열 이름을 문자열과 기본값으로 지정합니다. 그렇지 않으면 열이 있는 경우 열에 대한 참조를 반환합니다.

**구문**

`column_ifexists(`*columnName*`, `*기본값)*

**인수**

* *columnName*: 열의 이름
* *defaultValue*: 함수가 사용된 컨텍스트에 열이 없는 경우 사용할 값입니다.
                  이 값은 모든 스칼라 식(예: 다른 열에 대한 참조)일 수 있습니다.

**반환**

*columnName이* 있는 경우 참조하는 열입니다. 그렇지 않으면 - *기본값*.

**예**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```