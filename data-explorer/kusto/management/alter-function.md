---
title: . alter function-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: fc7c29542f63ea9b659bd3318d1442d9bca4ae48
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321712"
---
# <a name="alter-function"></a>.alter function

기존 함수를 변경 하 여 데이터베이스 메타 데이터 내부에 저장 합니다.
매개 변수 형식 및 CSL 문의 규칙은 [ `let` 문의](../query/letstatement.md)경우와 동일 합니다.

**구문**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|출력 매개 변수 |형식 |설명
|---|---|--- 
|Name  |String |함수의 이름입니다.
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 개 이상) `let` 문 다음에는 함수 호출 시 계산 되는 유효한 CSL 식이 나옵니다.
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 목적으로 사용 되는 함수에 대 한 설명입니다.

> [!NOTE]
> * 함수가 없는 경우 오류가 반환 됩니다. 새 함수를 만들려면를 참조 하십시오. [`.create function`](create-function.md)
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.
> * 처음에 함수를 만든 [데이터베이스 사용자](../management/access-control/role-based-authorization.md) 가 함수를 수정할 수 있습니다. 
> * 문에서 모든 Kusto 형식이 지원 되는 것은 아닙니다 `let` . 지원 되는 형식은 string, long, datetime, timespan 및 double입니다.
> * `skipvalidation`함수의 의미 체계 유효성 검사를 건너뛰려면를 사용 합니다. 이는 함수가 잘못 된 순서로 생성 되 고 F2를 사용 하는 F1이 이전에 만들어진 경우에 유용 합니다.
 
**예제** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Name |매개 변수 |본문|폴더|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|MyFolder|매개 변수가 있는 Demo 함수|
