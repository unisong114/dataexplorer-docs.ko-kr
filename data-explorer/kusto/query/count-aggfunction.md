---
title: count()(집계 함수) - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 count()(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513100"
---
# <a name="count-aggregation-function"></a>count()(집계 함수)

요약 그룹당 레코드 수를 반환합니다(또는 그룹화 없이 요약이 수행되는 경우 합계).

* [summarize](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.
* [countif](countif-aggfunction.md) 집계 함수를 사용하여 일부 조건자가 `true`를 반환하는 레코드만 계산합니다.

## <a name="syntax"></a>Syntax

`count()` 요약

## <a name="returns"></a>반환

요약 그룹당 레코드 수를 반환합니다(또는 그룹화 없이 요약이 수행되는 경우 합계).

## <a name="example"></a>예제

`W` 문자로 시작하는 상태의 이벤트 계산:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|시스템 상태|개수|
|---|---|
|웨스트버지니아|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|
