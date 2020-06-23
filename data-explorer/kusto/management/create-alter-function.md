---
title: . create 또는-alter function-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 create 또는 alter 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197245"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

저장 된 함수를 만들거나 기존 함수를 변경 하 여 데이터베이스 메타 데이터 내에 저장 합니다.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

제공 된 *FunctionName* 를 사용 하는 함수가 데이터베이스 메타 데이터에 없는 경우이 명령은 새 함수를 만듭니다. 그렇지 않으면 해당 함수가 변경 됩니다.

**예제**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Name|매개 변수|본문|폴더|DocString|
|---|---|---|---|---|
|TestFunction|(myLimit: int)|{StormEvents &#124; myLimit}를 사용 합니다.|MyFolder|매개 변수가 있는 Demo 함수|
