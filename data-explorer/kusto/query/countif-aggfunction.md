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
ms.openlocfilehash: 669978d8828f54926a8535f199ef7a9bc2ba7451
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515773"
---
# <a name="countif-aggregation-function"></a>countif () (집계 함수)

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

조건자 식 없이 행을 계산 하는- [count ()](count-aggfunction.md) 함수를 참조 하세요.

## <a name="syntax"></a>구문

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

