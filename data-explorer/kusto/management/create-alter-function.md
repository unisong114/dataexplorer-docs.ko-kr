---
title: .create-or-alter 기능 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .create-or-alter 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744279"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

저장된 함수를 만들거나 기존 함수를 변경하여 데이터베이스 메타데이터 내에 저장합니다.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

제공된 *FunctionName* 기능이 데이터베이스 메타데이터에 없는 경우 명령은 새 함수를 만듭니다. 함수가 이미 있으면 해당 함수가 변경됩니다.

**예제**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|이름|매개 변수|본문|폴더|닥스트링 (것)과 함께|
|---|---|---|---|---|
|테스트 기능|(myLimit:int)|{ 스톰이벤트 &#124; 마이리밋 }|마이 폴더|매개 변수가 있는 데모 기능|
