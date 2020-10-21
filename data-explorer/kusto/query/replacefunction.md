---
title: replace ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 replace ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d9fdcfaad41201cd99796c3f4966593aa7480e49
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243052"
---
# <a name="replace"></a>replace()

모든 정규식 일치 항목을 다른 문자열로 바꿉니다.

## <a name="syntax"></a>구문

`replace(`*regex* `,` *다시 작성* `,` *텍스트*`)`

## <a name="arguments"></a>인수

* *regex*: *텍스트*를 검색 하는 [정규식](https://github.com/google/re2/wiki/Syntax) 입니다. '('괄호')'에 캡처 그룹을 포함할 수 있습니다. 
* *재작성*: *matchingRegex*에서 수행 하는 모든 일치 항목에 대 한 대체 regex입니다. 전체 일치를 참조하려면 `\0`, 첫 번째 캡처 그룹을 참조하려면 `\1`, 이후 캡처 그룹을 참조하려면 `\2`를 사용하는 식입니다.
* *text*: 문자열입니다.

## <a name="returns"></a>반환

*regex*의 모든 일치 항목을 *rewrite*로 바꾼 후의 *text*입니다. 일치 항목은 겹치지 않습니다.

## <a name="example"></a>예제

이 문은:

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

다음과 같은 결과를 나타냄:

| x    | str | 대체됨|
|---|---|---|
| 1    | 숫자는 1.000000임  | 이전 숫자: 1.000000|
| 2    | 숫자는 2.000000임  | 이전 숫자: 2.000000|
| 3    | 숫자는 3.000000임  | 이전 숫자: 3.000000|
| 4    | 숫자는 4.000000임  | 이전 숫자: 4.000000|
| 5    | 숫자는 5.000000임  | 이전 숫자: 5.000000|
 