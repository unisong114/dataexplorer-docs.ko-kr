---
title: . alter function folder-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter function 폴더에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617836"
---
# <a name="alter-function-folder"></a>. alter 함수 폴더

기존 함수의 폴더 값을 변경 합니다.

`.alter``function` *FunctionName* FunctionName `folder` *폴더*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.
> * 처음에 함수를 만든 [데이터베이스 사용자](../management/access-control/role-based-authorization.md) 가 함수를 수정할 수 있습니다. 
> * 함수가 없는 경우 오류가 반환 됩니다. 새 함수를 만드는 경우 [create function](create-function.md)

|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 개 이상) 문을 사용 하 여 함수 호출 시 평가 되는 유효한 CSL 식을 입력 합니다.
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 목적으로 사용 되는 함수에 대 한 설명입니다.

**예제** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|속성 |매개 변수 |본문|폴더|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|업데이트 된 폴더|일부 DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|속성 |매개 변수 |본문|폴더|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|첫 번째 Level\Second 수준|일부 DocString|