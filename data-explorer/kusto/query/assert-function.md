---
title: assert ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 어설션 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 050974af47b0f5cd0e041694ee5f680b8c321614
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803321"
---
# <a name="assert"></a>assert()

조건을 확인합니다. 조건이 false 이면에서 오류 메시지를 출력 하 고 쿼리를 실패 합니다.

## <a name="syntax"></a>구문

`assert(`*조건* `, ` *메시지*`)`

## <a name="arguments"></a>인수

* *condition*: 평가할 조건 식입니다. 조건이 이면 지정 된 `false` 메시지를 사용 하 여 오류를 보고 합니다. 조건이 이면 `true` `true` 평가 결과로 반환 됩니다. 쿼리 분석 단계 중에는 조건을 상수로 평가 해야 합니다.
* *메시지*: 어설션이로 계산 되는 경우에 사용 되는 메시지 `false` 입니다. *메시지* 는 문자열 리터럴이어야 합니다.

> [!NOTE]
> `condition`쿼리 분석 단계에서 상수로 계산 되어야 합니다. 즉, 상수를 참조 하는 다른 식에서 생성 될 수 있으며 행 컨텍스트에 바인딩할 수 없습니다.

## <a name="returns"></a>반환

* `true`-조건이 인 경우`true`
* 조건이로 평가 되는 경우 의미 오류를 발생 시킵니다 `false` .

## <a name="examples"></a>예

다음 쿼리는 `checkLength()` 입력 문자열 길이를 확인 하 고를 사용 하 여 `assert` 입력 길이 매개 변수의 유효성을 검사 하는 함수를 정의 합니다 (0 보다 큰지 확인).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

이 쿼리를 실행 하면 오류가 발생 합니다.  
`assert() has failed with message: 'Length must be greater than zero'`


유효한 입력을 사용 하 여 실행 예 `len` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
