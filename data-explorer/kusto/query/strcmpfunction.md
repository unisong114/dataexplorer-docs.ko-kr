---
title: strcmp ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 strcmp ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5c1e4cb9a4ad77d0d48f52ca7e47fc6cea06cff4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243736"
---
# <a name="strcmp"></a>strcmp()

두 문자열을 비교합니다.

함수는 각 문자열의 첫 번째 문자 비교를 시작 합니다. 서로 같으면 문자가 다르거나 짧은 문자열의 끝에 도달할 때까지 다음 쌍으로 계속 됩니다.

## <a name="syntax"></a>구문

`strcmp(`*string1* `,` *문자열 2*`)` 

## <a name="arguments"></a>인수

* *string1*: 비교할 첫 번째 입력 문자열입니다. 
* *문자열 2*: 비교할 두 번째 입력 문자열입니다.

## <a name="returns"></a>반환

문자열 간의 관계를 나타내는 정수 값을 반환 합니다.
* *<0* -일치 하지 않는 첫 번째 문자의 string1에서 문자열 2 보다 낮은 값이 있습니다.
* *0* -두 문자열의 내용이 동일 합니다.
* *>0* -일치 하지 않는 첫 번째 문자가 문자열 2의 값 보다 큽니다.

## <a name="examples"></a>예

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|string1|string2|result|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|abcde...z|abc|1|