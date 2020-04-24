---
title: isnull() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 isnull()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513416"
---
# <a name="isnull"></a>isnull()

유일한 인수를 평가하고 인수가 `bool` null 값으로 평가되는지 여부를 나타내는 값을 반환합니다.

```kusto
isnull(parse_json("")) == true
```

**구문**

`isnull(`*Expr*`)`

**반환**

값이 null인지 여부에 따라 true 또는 false.

**참고 사항**

* `string`값은 null일 수 없습니다. [사용 은 형식의](./isemptyfunction.md) `string` 값이 비어 있는지 여부를 확인 하려면 비어 있습니다.

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

**예제**

```kusto
T | where isnull(PossiblyNull) | count
```