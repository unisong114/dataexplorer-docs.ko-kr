---
title: . create function-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 create 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0a509e33ca1bc4c6d4a400428898b7c241222a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320981"
---
# <a name="create-function"></a>.create function

지정 된 이름의 재사용 가능한 [ `let` 문](../query/letstatement.md) 함수인 저장 함수를 만듭니다. 함수 정의는 데이터베이스 메타 데이터와 함께 유지 됩니다.

함수는 다른 함수를 호출할 수 있습니다. (recursiveness는 지원 되지 않음) `let` *함수 본문* 의 일부로 문이 허용 됩니다. [ `let` 문을](../query/letstatement.md)참조 하세요.

매개 변수 형식 및 CSL 문의 규칙은 [ `let` 문의](../query/letstatement.md)경우와 동일 합니다.
    
**구문**

`.create``function`[ `ifnotexists` ] [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ] [ `,` `skipvalidation` `=` ' true '] `)` ] *FunctionName* `(` *ParamName* `:` *가 paramtype과* [ `,` ...] `)` `{` *functionbody*`}`

**출력**    
    
|출력 매개 변수 |형식 |설명
|---|---|--- 
|Name  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 개 이상) `let` 문 다음에는 함수 호출 시 계산 되는 유효한 CSL 식이 나옵니다.
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 목적으로 사용 되는 함수에 대 한 설명입니다.

> [!NOTE]
> * 함수가 이미 있는 경우:
>    * 플래그가 지정 된 경우에는 `ifnotexists` 명령이 무시 되 고 변경 내용이 적용 되지 않습니다.
>    * `ifnotexists`플래그가 지정 되지 않은 경우 오류가 반환 됩니다.
>    * 기존 함수를 변경 하는 방법에 대 한 자세한 내용은 [`.alter function`](alter-function.md)
> * [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
> * 문에서 모든 데이터 형식이 지원 되는 것은 아닙니다 `let` . 지원 되는 형식은 boolean, string, long, datetime, timespan, double 및 dynamic입니다.
> * ' Skipvalidation '을 사용 하 여 함수의 의미 체계 유효성 검사를 건너뜁니다. 이는 함수가 잘못 된 순서로 생성 되 고 F2를 사용 하는 F1이 이전에 만들어진 경우에 유용 합니다.

**예제** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Name|매개 변수|본문|폴더|DocString|
|---|---|---|---|---|
|MyFunction1|()|{StormEvents &#124; 제한 100}|데모|Simple demo 함수|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Name|매개 변수|본문|폴더|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit: long)|{StormEvents &#124; 제한 myLimit}|데모|매개 변수가 있는 Demo 함수|
