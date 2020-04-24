---
title: strcmp() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 strcmp()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506905"
---
# <a name="strcmp"></a>strcmp()

두 문자열을 비교합니다.

함수는 각 문자열의 첫 번째 문자를 비교하기 시작합니다. 서로 같으면 문자가 다를 때까지 또는 짧은 문자열의 끝에 도달할 때까지 다음 쌍으로 계속됩니다.

**구문**

`strcmp(`*문자열1* `,` *문자열2*`)` 

**인수**

* *string1*: 비교를 위한 첫 번째 입력 문자열입니다. 
* *string2*: 비교를 위한 두 번째 입력 문자열입니다.

**반환**

문자열 간의 관계를 나타내는 정수 값을 반환합니다.
* *<0* - 일치하지 않는 첫 번째 문자는 string2보다 string1의 값이 낮습니다.
* *0* - 두 문자열의 내용이 동일합니다.
* *>0* - 일치하지 않는 첫 번째 문자는 string2보다 string1에서 더 큰 값을 가있습니다.

**예**

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
|Abcde|abc|1|