---
title: .show 기능 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .show 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7acfdb96570b067b0461f5a788b55fc8274c03f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519842"
---
# <a name="show-functions"></a>.show 기능

현재 선택한 데이터베이스에 저장된 모든 함수를 나열합니다.
하나의 특정 함수만 반환하려면 [.show 함수를](#show-function)참조하십시오.

```
.show functions
```

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.
 
|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방식을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.
 
**출력 예제** 

|이름 |매개 변수|본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능1 |() | {스톰이벤트 &#124; 제한 100}|마이 폴더|간단한 데모 기능|
|마이기능2 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|마이 폴더|매개 변수가 있는 데모 기능|
|마이기능3 |() | { 스톰 이벤트(100) }|마이 폴더|다른 기능을 호출하는 함수||

## <a name="show-function"></a>.show 기능

```
.show function MyFunc1
```

저장된 함수 중 하나에 대한 세부 정보를 나열합니다. **모든** 함수 목록은 [.show 함수를](#show-functions)참조하십시오.

**구문**

`.show``function` *함수 이름*

**출력**

|출력 매개 변수 |Type |Description
|---|---|--- 
|속성  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 이상) `let` 함수 호출 시 평가되는 유효한 CSL 식이 뒤따릅니다.
|폴더|String|UI 함수 분류에 사용되는 폴더입니다. 이 매개 변수는 함수가 호출되는 방식을 변경하지 않습니다.
|닥스트링 (것)과 함께|String|UI 를 위한 함수에 대한 설명입니다.
 
> [!NOTE] 
> * 함수가 없으면 오류가 반환됩니다.
> * [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.
 
**예제** 

```
.show function MyFunction1 
```
    
|이름 |매개 변수 |본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이기능1 |() | {스톰이벤트 &#124; 제한 100}|마이 폴더|간단한 데모 기능