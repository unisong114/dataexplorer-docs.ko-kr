---
title: guid 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 guid 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509863"
---
# <a name="the-guid-data-type"></a>guid 데이터 형식

`guid` ()`uuid` `uniqueid`데이터 형식은 128비트 전역고유 값을 나타냅니다.

> [!WARNING]
> 이 문서를 작성할 당시에는 `guid` 형식에 대한 지원이 완전하지 않습니다.
> 주요 간격은 이 형식의 열에 인덱스가 없기 때문에 이 형식에 대해 조건부로 입력하는 쿼리의 성능에 영향을 미칩니다.
> 대신 팀에서 `string` 형식의 값을 사용하는 것이 좋습니다.

## <a name="guid-literals"></a>guid 리터럴

문자 그대로 형식을 `guid`나타내려면 다음 형식을 사용합니다.

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

특수 양식은 `guid(null)` [null 값을](null-values.md)나타냅니다.