---
title: array_iif() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 array_iif()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663909"
---
# <a name="array_iif"></a>array_iif()

동적 배열에 요소 별 iif 기능.

또 다른 별칭: array_iff().

**구문**

`array_iif(`*조건 배열*, *IfTrue*, *Iffalse*]`)`

**인수**

* *conditionArray*: *부울* 또는 숫자 값의 입력 배열은 동적 배열이어야 합니다.
* *ifTrue*: 값 또는 기본 값의 입력 배열 - *조건배열의* 해당 값이 *true일*때 결과 값입니다.
* *ifFalse*: 값 또는 기본 값의 입력 배열 - *조건배열의* 해당 값이 *false일*때 결과 값입니다.

**참고 사항**

* 결과 길이는 *조건의 길이배열*.
* 숫자 조건 값은 *조건으로* 처리됩니다!= *0*.
* 비숫자/null 조건 값은 결과의 해당 인덱스에 null을 갖습니다.
* 누락된 값(길이이 짧은 배열)은 null로 처리됩니다.

**반환**

조건 배열의 해당 값에 따라 *IfTrue* 또는 *IfFalse* [배열] 값에서 가져온 값의 동적 배열입니다.

**예제**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition(조건)|l|r|res|
|---|---|---|---|
|[참, 거짓, 참]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
