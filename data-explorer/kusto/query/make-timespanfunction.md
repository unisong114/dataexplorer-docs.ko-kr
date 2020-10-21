---
title: make_timespan ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_timespan ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93b8ed12bcfb83c39964f820e61f928357af62c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253185"
---
# <a name="make_timespan"></a>make_timespan()

지정 된 기간에서 [timespan](./scalar-data-types/timespan.md) 스칼라 값을 만듭니다.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>구문

`make_timespan(`*시간*,*분*`)`

`make_timespan(`*시*,*분*,*초*`)`

`make_timespan(`*일*,*시*,*분*,*초*`)`

## <a name="arguments"></a>인수

* *day*: day (양의 정수 값)
* *시간*: 시간 (0에서 23 사이의 정수 값)
* *분*: 분 (0에서 59 사이의 정수 값)
* *second*: second (0에서 59.9999999 사이의 실제 값)

## <a name="returns"></a>반환

만들기가 완료 되 면 결과는 [timespan](./scalar-data-types/timespan.md) 값이 되 고, 그렇지 않으면 결과는 null이 됩니다.
 
## <a name="example"></a>예제

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|timespan|
|---|
|1.12:30:55.1230000|


