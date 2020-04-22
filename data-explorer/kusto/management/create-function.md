---
title: .create 함수 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .create 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744468"
---
# <a name="create-function"></a>.create function

저장된 함수를 만듭니다. [ `let` ](../query/letstatement.md) 함수 정의는 데이터베이스 메타데이터와 함께 유지됩니다.

함수는 다른 함수를 호출할 수 있으며(재귀는 `let` 지원되지 않음) 명령문은 *Function Body의*일부로 허용됩니다. [ `let` 문을](../query/letstatement.md)참조하십시오.

매개 변수 형식 및 CSL 문에 대한 규칙은 [ `let` 문과](../query/letstatement.md)동일합니다.
    
**구문**

`.create``function` [`ifnotexists`[`with` `(``docstring` `=` [ *[ [ 문서*`,` `skipvalidation` `=` ] [ `)`폴더`,` `folder` `=` *이름* `)`] [ [`,` [ [ [ [ [ [ [ [ [ [ [ [ ] [ [ [ [ [ ] [ [ [ ] [ [ ] [ [ ] [ [ ] [ [ ] [ [ ] [ [ ] [ ] [ ] [ ] [ [ ] [ [ ] [ [ ] [ [ ] [ ] [ [ ] [ [ [ 함수 *이름* `(` *ParamName* `:` *Para* `)` *바디* `{``}`

**출력**    
    
|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방식을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.

> [!NOTE]
> * 함수가 이미 있는 경우:
>    * 플래그를 지정하면 `ifnotexists` 명령이 무시됩니다(변경 적용 없음).
>    * 플래그를 지정하지 않으면 `ifnotexists` 오류가 반환됩니다.
>    * 기존 함수를 변경하면 [.alter 함수를 참조하십시오.](alter-function.md)
> * [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.
> * 모든 데이터 형식이 `let` 명령문에서 지원되는 것은 아닙니다. 지원되는 형식은 부울, 문자열, 긴, 날짜 시간, 시간 범위, 이중 및 동적입니다.
> * '건너뛰기 유효성 검사'를 사용하여 함수의 의미 체계 유효성 검사를 건너뜁니다. 이 기능은 잘못된 순서로 만들어지고 F2를 사용하는 F1이 이전에 만들어지는 경우에 유용합니다.

**예** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|이름|매개 변수|본문|폴더|닥스트링 (것)과 함께|
|---|---|---|---|---|
|마이기능1|()|{스톰이벤트 &#124; 제한 100}|데모|간단한 데모 기능|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|이름|매개 변수|본문|폴더|닥스트링 (것)과 함께|
|---|---|---|---|---|
|마이기능2|(myLimit:long)|{스톰이벤트 &#124; 제한 myLimit}|데모|매개 변수가 있는 데모 기능|
