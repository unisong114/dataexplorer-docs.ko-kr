---
title: .alter 기능 폴더 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .alter 함수 폴더에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 140991c723ebdd12fa17000ea845adbbfdd27771
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522545"
---
# <a name="alter-function-folder"></a>.alter 기능 폴더

기존 함수의 폴더 값을 변경합니다.

`.alter``function` *함수 이름* `folder` *폴더*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.
> * 함수를 처음 만든 [데이터베이스 사용자는](../management/access-control/role-based-authorization.md) 함수를 수정할 수 있습니다. 
> * 함수가 없으면 오류가 반환됩니다. 새 함수를 만들려면 [.create 함수](create-function.md)

|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) 함수 호출 시 평가되는 유효한 CSL 식 다음에 문 앞에 놓습니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방법을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.

**예제** 

```
.alter function MyFunction1 folder "Updated Folder"
```
    
|이름 |매개 변수 |본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능2 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|업데이트된 폴더|일부 문서 스트링|

```
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|이름 |매개 변수 |본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능2 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|첫 번째 레벨\두 번째 수준|일부 문서 스트링|