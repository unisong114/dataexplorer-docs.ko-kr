---
title: count () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 count () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.openlocfilehash: a5f5f554373331d66a08e7166249e8e24c4fbd7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348788"
---
# <a name="count-aggregation-function"></a>count () (집계 함수)

요약 그룹당 레코드 수를 반환 합니다 (그룹화 하지 않고 요약이 수행 되는 경우 합계).

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.
* [Countif](countif-aggfunction.md) 집계 함수를 사용 하 여 일부 조건자가 반환 하는 레코드만 계산 `true` 합니다.

## <a name="syntax"></a>구문

함수`count()`

## <a name="returns"></a>반환

요약 그룹당 레코드 수를 반환 합니다 (그룹화 하지 않고 요약이 수행 되는 경우 합계).

## <a name="example"></a>예제

문자로 시작 하는 상태의 이벤트 계산 `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|상태|개수|
|---|---|
|웨스트버지니아|757|
|와이오밍|396|
|워싱턴|261|
|위스콘신|1850|
