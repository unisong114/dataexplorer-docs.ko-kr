---
title: Timespan 데이터 형식-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 timespan 데이터 형식에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610324"
---
# <a name="the-timespan-data-type"></a>Timespan 데이터 형식입니다.

`timespan`( `time` ) 데이터 형식은 시간 간격을 나타냅니다.

## <a name="timespan-literals"></a>timespan 리터럴

형식의 리터럴에는 `timespan` `timespan(` *value* `)` 다음 표에 표시 된 것과 같이 *값*에 대해 많은 형식이 지원 되는 구문 값이 있습니다.

|값|시간 길이|
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

특수 폼은 `time(null)` [null 값](null-values.md)입니다.

## <a name="timespan-operators"></a>timespan 연산자

형식의 두 값 `timespan` 을 추가, 빼기 및 나눌 수 있습니다.
마지막 작업은 `real` 한 값이 다른 값에 맞출 수 있는 소수 자릿수를 나타내는 형식의 값을 반환 합니다.

## <a name="examples"></a>예제

다음 예에서는 몇 가지 방법으로 하루 동안의 초 수를 계산 합니다.

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```