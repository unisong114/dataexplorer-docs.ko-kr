---
title: now ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 now ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249808"
---
# <a name="now"></a>now()

지정 된 시간 범위에 따라 선택적으로 오프셋 되는 현재 UTC 시계 시간을 반환 합니다.
이 함수를 문에 여러 번 사용할 수 있으며 참조하는 시계 시간은 모든 인스턴스에 대해 동일합니다.

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>구문

`now(`[*오프셋*]`)`

## <a name="arguments"></a>인수

* *offset*: `timespan` 현재 UTC 클록 시간에 추가 된입니다. 기본값: 0.

## <a name="returns"></a>반환

`datetime`(으)로 반환되는 현재 UTC 시계 시간입니다.

`now()` + *이동* 

## <a name="example"></a>예제

조건자에 의해 식별된 이벤트 이후 간격을 결정;

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```