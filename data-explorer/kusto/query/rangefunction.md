---
title: range ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 범위 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a37d375ca83252b063821659f0b5490337c6667
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241058"
---
# <a name="range"></a>range()

동일 하 게 간격이 있는 일련의 값을 포함 하는 동적 배열을 생성 합니다.

## <a name="syntax"></a>구문

`range(`*시작* `,` *중지*[ `,` *step*]`)` 

## <a name="arguments"></a>인수

* *start*: 결과 배열의 첫 번째 요소 값입니다. 
* *stop*: 결과 배열의 마지막 요소 값 또는 결과 배열의 마지막 요소 보다 큰 값 또는 *start*에서 *단계의* 정수 배수 내에서 가장 큰 값입니다.
* *단계*: 배열의 두 연속 요소 간 차이입니다. *Step* 의 기본값은 `1` 숫자 및 `1h` 의 `timespan` 경우입니다.`datetime`

## <a name="examples"></a>예

다음 예제는 `[1, 4, 7]`을 반환합니다.

```kusto
T | extend r = range(1, 8, 3)
```

다음 예제는 2015 년의 모든 날짜를 저장하는 배열을 반환합니다.

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

다음 예제는 `[1,2,3]`을 반환합니다.

```kusto
range(1, 3)
```

다음 예제는 `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`을 반환합니다.

```kusto
range(1h, 5h)
```
