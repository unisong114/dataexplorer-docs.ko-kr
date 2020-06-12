---
title: strcat ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 strcat ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717107"
---
# <a name="strcat"></a>strcat()

1에서 64 사이의 인수를 연결 합니다.

* 인수가 문자열 형식이 아니면 문자열이 강제로 문자열로 변환 됩니다.

**구문**

`strcat(`*인수 1*, *인수 2*[, *argumentn*]`)`

**인수**

* *인수 1* ... *argumentn*: 연결할 식입니다.

**반환**

단일 문자열에 연결 된 인수입니다.

**예**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
