---
title: .alter 함수 문서 문자열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .alter 함수 문서 문자열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522579"
---
# <a name="alter-function-docstring"></a>.alter 함수 문서 문자열

기존 함수의 DocString 값을 변경합니다.

`.alter``function` *함수 이름* `docstring` *설명서*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.
> * 함수를 처음 만든 [데이터베이스 사용자는](../management/access-control/role-based-authorization.md) 함수를 수정할 수 있습니다. 
> * 함수가 없으면 오류가 반환됩니다. 새 함수를 만들려면 [.create 함수를 참조하십시오.](create-function.md)

|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방식을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.

**예제** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|이름 |매개 변수 |본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능2 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|마이 폴더|업데이트된 문서 문자열|