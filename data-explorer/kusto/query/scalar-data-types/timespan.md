---
title: 시간 범위 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 시간 범위 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509608"
---
# <a name="the-timespan-data-type"></a>시간 범위 데이터 형식

`timespan` ()`time`데이터 형식은 시간 간격을 나타냅니다.

## <a name="timespan-literals"></a>시간 범위 리터럴

문자 `timespan` 문자에는 구문 `timespan(` *값이*`)`있으며 다음 표에 표시된 대로 값에 대해 여러 형식이 지원됩니다. *value*

|||
---|---
`2d`|2일
`1.5h`|1.5시간
`30m`|30분
`10s`|10초
`0.1s`|0.1초
`100ms`| 100밀리초
`10microsecond`|10 마이크로초
`1tick`|100나노초
`time(15 seconds)`|15초
`time(2)`| 2일
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

특수 양식은 `time(null)` [null 값입니다.](null-values.md)

## <a name="timespan-operators"></a>시간 범위 연산자

형식의 `timespan` 두 값을 추가, 빼기 및 분할할 수 있습니다.
마지막 연석은 한 `real` 값이 다른 값에 맞을 수 있는 소수 수를 나타내는 형식 값을 반환합니다.

## <a name="examples"></a>예

다음 예제에서는 여러 가지 방법으로 하루에 있는 초 수를 계산합니다.

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```