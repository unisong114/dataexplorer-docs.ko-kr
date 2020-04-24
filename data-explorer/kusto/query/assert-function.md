---
title: 어설션() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 어설션()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518414"
---
# <a name="assert"></a>assert()

조건을 확인합니다. 조건이 false이면 오류 메시지를 출력하고 쿼리에 실패합니다.

**구문**

`assert(`*조건*`, `*메시지*`)`

**인수**

* *조건*: 평가할 조건식입니다. 조건이 `false`있는 경우 지정된 메시지를 사용하여 오류를 보고합니다. 조건이 `true`있으면 평가 결과로 `true` 반환됩니다. 조건은 쿼리 분석 단계에서 일정하게 평가되어야 합니다.
* *메시지*: 어설션이 평가될 때 사용되는 메시지입니다. `false` *메시지는* 문자열 리터럴이어야 합니다.


**반환**

* `true`- 조건이`true`
* 조건을 평가하는 경우 의미 체계 오류가 `false`발생합니다.

**참고 사항**

* `condition`쿼리 분석 단계에서 상수로 평가되어야 합니다. 즉, 상수를 참조 하는 다른 식에서 생성될 수 있으며 행 컨텍스트에 바인딩할 수 없습니다.

**예**

다음 쿼리는 입력 `checkLength()` 문자열 길이를 검사하는 `assert` 함수를 정의하고 입력 길이 매개 변수의 유효성을 검사하는 데 사용합니다(0보다 큰지 확인).

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

이 쿼리를 실행하면 다음과 같은 오류가 발생합니다.  
`assert() has failed with message: 'Length must be greater than zero'`


유효한 `len` 입력으로 실행의 예:

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|input|
|---|
|4567|