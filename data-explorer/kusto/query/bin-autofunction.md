---
title: bin_auto ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 bin_auto ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dba71c3b9b52a4edaf3a9b1260f56fc94eb935e3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245440"
---
# <a name="bin_auto"></a>bin_auto()

쿼리 속성에서 제공 하는 bin 크기 및 시작 지점에 대 한 제어를 사용 하 여 값을 고정 크기 "bin"으로 내림 합니다.

## <a name="syntax"></a>구문

`bin_auto``(` *식*`)`

## <a name="arguments"></a>인수

* *Expression*: 반올림할 값을 나타내는 숫자 형식의 스칼라 식입니다.

**클라이언트 요청 속성**

* `query_bin_auto_size`: 각 bin의 크기를 나타내는 숫자 리터럴입니다.
* `query_bin_auto_at`: "Fixed point" (즉, 값) 인 *식* 의 한 값을 나타내는 숫자 리터럴입니다 `fixed_point` `bin_auto(fixed_point)` == `fixed_point` .

## <a name="returns"></a>반환

아래 식의 가장 가까운 배수 `query_bin_auto_at` 이며, 그 *Expression* `query_bin_auto_at` 자체로 변환 될 수 있도록 이동 했습니다.

## <a name="examples"></a>예

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|타임스탬프                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |