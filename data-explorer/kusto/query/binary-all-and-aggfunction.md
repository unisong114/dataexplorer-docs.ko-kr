---
title: binary_all_and () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 binary_all_and () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9086d00ecbc800174ce2b9cda2b4ae1ba59d52b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349145"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (집계 함수)

요약 그룹당 이항 연산을 사용 하 여 값을 누적 `AND` 합니다 (그룹화 하지 않고 요약이 수행 되는 경우 전체).

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`binary_all_and(` *식* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: long 숫자입니다.

## <a name="returns"></a>반환

요약 그룹당 레코드에 대해 이항 연산을 사용 하 여 집계 된 값을 반환 `AND` 합니다. 요약을 그룹화 하지 않고 요약 하는 경우에는 합계를 사용 합니다.

## <a name="example"></a>예제

이항 연산을 사용 하 여 ' 카페 ' 생성 `AND` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|result|
|---|
|CAFEF00D|
