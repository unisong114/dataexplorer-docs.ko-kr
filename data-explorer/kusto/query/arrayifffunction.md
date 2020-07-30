---
title: array_iif ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 array_iif ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 1844d87dffb5ac0046c3f62600b8c4914dc93a89
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349587"
---
# <a name="array_iif"></a>array_iif()

동적 배열의 요소 단위 iif 함수

다른 별칭: array_iff ().

## <a name="syntax"></a>구문

`array_iif(`*Conditionarray*, *iftrue*, *IfFalse*]`)`

## <a name="arguments"></a>인수

* *conditionarray*: *부울* 또는 숫자 값의 입력 배열은 동적 배열 이어야 합니다.
* *Iftrue*: 값 또는 기본 값의 입력 배열- *conditionarray* 의 해당 값이 *true*인 경우의 결과 값입니다.
* *ifFalse*: 값 또는 기본 값의 입력 배열- *conditionarray* 의 해당 값이 *false*인 경우의 결과 값입니다.

**참고**

* 결과 길이는 *Conditionarray*의 길이입니다.
* 숫자 조건 값은 *조건* ! = *0*으로 처리 됩니다.
* 숫자가 아닌/null 조건 값은 결과의 해당 인덱스에 null이 포함 됩니다.
* 누락 값 (길이가 짧은 배열)은 null로 처리 됩니다.

## <a name="returns"></a>반환

조건 배열의 해당 값에 따라 *Iftrue* 또는 *IfFalse* [array] 값에서 가져온 값의 동적 배열입니다.

## <a name="example"></a>예제

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition(조건)|l|r|res|
|---|---|---|---|
|[true, false, true]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
