---
title: strcat() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 strcat()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506922"
---
# <a name="strcat"></a>strcat()

1인수와 64개의 인수를 연결합니다.

* 인수가 문자열 형식이 아닌 경우 강제로 문자열로 변환됩니다.

**구문**

`strcat(`*인수1*,*인수2* [, *인수N]*`)`

**인수**

* *인수1* ... *argumentn* : 통합할 식입니다.

**반환**

인수, 단일 문자열에 연결 됩니다.

**예**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|