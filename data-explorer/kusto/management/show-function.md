---
title: . show 함수-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에 표시 되는 함수를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d4f4fcdbe60975b8b1c3369a364f062b223f9c6
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967369"
---
# <a name="show-functions"></a>. show 함수

현재 선택 된 데이터베이스에 있는 모든 저장 된 함수를 나열 합니다.
하나의 특정 함수만 반환 하려면 [. show 함수](#show-function)를 참조 하세요.

## <a name="show-functions"></a>. show 함수

```kusto
.show functions
```

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
 
|출력 매개 변수 |Type |Description
|---|---|--- 
|Name  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 개 이상) `let` 문 다음에는 함수 호출 시 계산 되는 유효한 CSL 식이 나옵니다.
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 목적으로 사용 되는 함수에 대 한 설명입니다.
 
**출력 예제** 

|Name |매개 변수|본문|폴더|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 제한 100}|MyFolder|Simple demo 함수|
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|MyFolder|매개 변수가 있는 Demo 함수|
|MyFunction3 |() | {StormEvents (100)}|MyFolder|다른 함수를 호출 하는 함수||

## <a name="show-function"></a>.show function

```kusto
.show function MyFunc1
```

하나의 특정 저장 된 함수에 대 한 세부 정보를 나열 합니다. **모든** 함수 목록은를 참조 하십시오 [. 함수 표시](#show-functions)

**구문**

`.show``function` *FunctionName*

**출력**

|출력 매개 변수 |Type |Description
|---|---|--- 
|Name  |String |함수의 이름입니다. 
|매개 변수  |String |함수에 필요한 매개 변수입니다.
|본문  |String |(0 개 이상) `let` 문 다음에는 함수 호출 시 계산 되는 유효한 CSL 식이 나옵니다.
|폴더|String|UI 함수 분류에 사용 되는 폴더입니다. 이 매개 변수는 함수가 호출 되는 방식을 변경 하지 않습니다.
|DocString|String|UI 목적으로 사용 되는 함수에 대 한 설명입니다.
 
> [!NOTE] 
> * 함수가 없는 경우 오류가 반환 됩니다.
> * [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
 
**예제** 

```kusto
.show function MyFunction1 
```
    
|Name |매개 변수 |본문|폴더|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 제한 100}|MyFolder|Simple demo 함수
