---
title: countif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 countif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614981"
---
# <a name="countif-aggregation-function"></a>countif () (집계 함수)

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다. [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

요약 `countif(` *조건자*`)`

## <a name="arguments"></a>인수

*Predicate*: 집계 계산에 사용 되는 식입니다. *조건자* 는 부울 (true/false로 계산)의 반환 형식을 사용 하는 스칼라 식일 수 있습니다.

## <a name="returns"></a>반환

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

## <a name="example"></a>예제

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

## <a name="see-also"></a>참조

[count ()](count-aggfunction.md) 함수는 조건자 식이 없는 행을 계산 합니다.
