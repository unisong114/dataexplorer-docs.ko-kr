---
title: . alter function docstring-Azure 데이터 탐색기
description: 이 문서에서는 `.alter function docstring` Azure 데이터 탐색기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cad374c767b126d60b7c701f596bddf3c20c4345
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763877"
---
# <a name="alter-function-docstring"></a>. alter 함수 docstring

`DocString`기존 함수의 값을 변경 합니다.

`.alter``function` *FunctionName* `docstring` *설명서*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.
> * 처음에 함수를 만든 [데이터베이스 사용자](../management/access-control/role-based-authorization.md) 가 함수를 수정할 수 있습니다.
> * 함수가 없는 경우 오류가 반환 됩니다. 새 함수를 만드는 방법에 대 한 자세한 내용은 [create function](create-function.md)을 참조 하세요.

|출력 매개 변수 |형식 |설명
|---|---|--- 
|Name  |String |함수의 이름입니다.
|매개 변수  |String |함수에 필요한 매개 변수
|본문  |String |(0 개 이상) `let` 함수를 호출할 때 평가 되는 유효한 CSL 식 뒤에 오는 문
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 용도에 대 한 함수 설명

**예제** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Name |매개 변수 |본문|폴더|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|MyFolder|업데이트 된 docstring|
