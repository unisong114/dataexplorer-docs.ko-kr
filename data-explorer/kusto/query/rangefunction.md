---
title: 범위() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 range()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86558591e6312edd218230cda19a4afc17a13b27
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744571"
---
# <a name="range"></a>range()

일련의 균등 한 간격 값을 보유 하는 동적 배열을 생성 합니다.

**구문**

`range(`*시작* `,` *stop*중지`,` [ *단계*]`)` 

**인수**

* *시작*: 결과 배열의 첫 번째 요소의 값입니다. 
* *stop*: 결과 배열의 마지막 요소 값 또는 결과 배열의 마지막 요소보다 큰 최소 값과 *시작부터* *단계의* 정수 배수 내에 있습니다.
* *단계*: 배열의 두 연속 요소 간의 차이입니다. *단계의* 기본값은 `1` 숫자및 `1h` `timespan``datetime`

**예**

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
