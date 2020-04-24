---
title: .alter 기능 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .alter 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522460"
---
# <a name="alter-function"></a>.alter 기능

기존 함수를 변경하고 데이터베이스 메타데이터 내에 저장합니다.
매개 변수 형식 및 CSL 문에 대한 규칙은 [ `let` 문과](../query/letstatement.md)동일합니다.

**구문**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다.
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방법을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.

> [!NOTE]
> * 함수가 없으면 오류가 반환됩니다. 새 함수를 만들려면 [.create 함수를 참조하십시오.](create-function.md)
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.
> * 함수를 처음 만든 [데이터베이스 사용자는](../management/access-control/role-based-authorization.md) 함수를 수정할 수 있습니다. 
> * 모든 Kusto 형식이 `let` 명령문에서 지원되는 것은 아닙니다. 지원되는 형식은 문자열, 긴 형식, 기간, 시간 범위 및 double입니다.
> * 함수의 의미 체계 유효성 검사를 건너뛰는 데 사용합니다. `skipvalidation` 이 기능은 잘못된 순서로 만들어지고 F2를 사용하는 F1이 이전에 만들어지는 경우에 유용합니다.
 
**예제** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|이름 |매개 변수 |본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능2 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|마이 폴더|매개 변수가 있는 데모 기능|