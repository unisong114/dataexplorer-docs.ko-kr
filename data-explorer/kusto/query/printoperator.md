---
title: 인쇄 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 인쇄 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: 19fa7a22a4f26d7d66a6224b4943f7ed976b531f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249587"
---
# <a name="print-operator"></a>print 연산자

하나 이상의 스칼라 식으로 단일 행을 출력 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>구문

`print` [*ColumnName* `=` ] *ScalarExpression* [', ' ...]

## <a name="arguments"></a>인수

* *ColumnName*: 출력의 단일 열에 할당할 옵션 이름입니다.
* *ScalarExpression*: 평가할 스칼라 식입니다.

## <a name="returns"></a>반환

단일 셀에 계산 된 *ScalarExpression*의 값이 있는 단일 열 단일 행 테이블입니다.

## <a name="examples"></a>예

`print`연산자는 하나 이상의 스칼라 식을 계산 하 고 결과 값에서 단일 행 테이블을 만드는 빠른 방법으로 유용 합니다.
예:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
