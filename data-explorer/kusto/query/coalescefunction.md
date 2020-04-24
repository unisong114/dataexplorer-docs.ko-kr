---
title: 병합() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 병합()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517139"
---
# <a name="coalesce"></a>coalesce()

식 목록을 평가하고 첫 번째 비null(또는 문자열에 대해 비어 없는) 식을 반환합니다.

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**구문**

`coalesce(`*expr_1*`, `*expr_2* `,` ...)

**인수**

* *expr_i*: 평가할 스칼라 식입니다.
- 모든 인수는 동일한 형식이어야 합니다.
- 최대 64개의 인수가 지원됩니다.


**반환**

값이 null이 아닌 첫 *번째 expr_i* 값(또는 문자열 식의 경우 비어 있지 않음)입니다.

**예제**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|