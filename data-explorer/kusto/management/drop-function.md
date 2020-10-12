---
title: . drop function-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 drop 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954742"
---
# <a name="drop-function"></a>.drop function

데이터베이스에서 함수를 삭제 합니다.
데이터베이스에서 여러 함수를 삭제 하는 방법에 대해서는 [drop 함수](#drop-functions)를 참조 하십시오.

**구문**

`.drop``function` *FunctionName* [ `ifexists` ]

* `ifexists`: 지정 된 경우 존재 하지 않는 함수에 대 한 명령의 동작을 실패 하지 않도록 수정 합니다.

> [!NOTE]
> * [Function admin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
    
|출력 매개 변수 |유형 |설명
|---|---|--- 
|Name  |String |제거 된 함수의 이름
 
**예제** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. drop 함수

데이터베이스에서 여러 함수를 삭제 합니다.

**구문**

`.drop``functions`(*FunctionName1*, *FunctionName2*,..) [ifexists]

**반환**

이 명령은 데이터베이스의 나머지 함수 목록을 반환 합니다.

|출력 매개 변수 |유형 |설명
|---|---|--- 
|Name  |String |함수의 이름입니다. 
|매개 변수  |문자열 |함수에 필요한 매개 변수입니다.
|본문  |문자열 |(0 개 이상) `let` 문 다음에는 함수 호출 시 계산 되는 유효한 CSL 식이 나옵니다.
|폴더|문자열|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|문자열|UI 목적으로 사용 되는 함수에 대 한 설명입니다.

[Function admin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**예제** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
