---
title: hash_many() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 hash_many()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514181"
---
# <a name="hash_many"></a>hash_many()

여러 값의 결합된 해시 값을 반환합니다.

**구문**

`hash_many(`*s1* `,` *s2* `,` *[s3* ...]`)`

**인수**

* *s1*, *s2*, ..., *sN*: 함께 해시 될 입력 값.

**반환**

지정된 스칼라의 결합된 해시 값입니다.

**예**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|결합|
|---|---|---|
|안녕하세요.|World|-1440138333540407281|