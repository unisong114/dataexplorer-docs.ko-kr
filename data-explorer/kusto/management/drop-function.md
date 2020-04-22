---
title: .drop 함수 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .drop 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744779"
---
# <a name="drop-function"></a>.drop function

데이터베이스에서 함수를 삭제합니다.
데이터베이스에서 여러 함수를 삭제하는 경우 [.drop 함수를](#drop-functions)참조하십시오.

**구문**

`.drop``function` *함수* 이름`ifexists`[ ]

* `ifexists`: 지정하면 존재하지 않는 함수에 대해 실패하지 않도록 명령의 동작을 수정합니다.

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.
> * 함수를 처음 만든 [데이터베이스 사용자는](../management/access-control/role-based-authorization.md) 함수를 수정할 수 있습니다.  
    
|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |제거된 함수의 이름입니다.
 
**예제** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>.drop 기능

데이터베이스에서 여러 함수를 삭제합니다.

**구문**

`.drop``functions` *(함수이름1,* *함수네임2 ,..)* [존재하는 경우]

**반환**

이 명령은 데이터베이스에 남아 있는 함수 목록을 반환합니다.

|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방식을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.

[기능 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**예제** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
