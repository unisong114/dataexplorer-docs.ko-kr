---
title: bag_merge ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_merge ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 66a05cdc03b155b8fceace0af8d86807a10d0da4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349366"
---
# <a name="bag_merge"></a>bag_merge()

`dynamic` `dynamic` 모든 속성이 병합 된 속성 모음에 속성 모음을 병합 합니다.

## <a name="syntax"></a>Syntax

`bag_merge(`*bag1* `, ` *bag2* `[` ,` *bag3*, ...])`

## <a name="arguments"></a>인수

* *bag1 ... bagN*: 입력 `dynamic` 속성-백. 함수는 2 ~ 64 개의 인수를 허용 합니다.

## <a name="returns"></a>반환

`dynamic`속성 모음을 반환 합니다. 모든 입력 속성 모음 개체를 병합 한 결과 키가 두 개 이상의 입력 개체에 표시 되는 경우이 키에 사용할 수 있는 값 중에서 임의의 값이 선택 됩니다.

## <a name="example"></a>예제

식:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|result|
|---|
|{<br>  "A1": 12,<br>  "B1": 2,<br>  "C1": 3,<br>  "A2": 81,<br>  "B2": 82<br>}|
