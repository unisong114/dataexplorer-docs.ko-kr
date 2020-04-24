---
title: hash_combine() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_combine()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514266"
---
# <a name="hash_combine"></a>hash_combine()

두 개 이상의 해시의 해시 값을 결합합니다.

**구문**

`hash_combine(`*h1* `,` *h2* `,` *[h3* ...]`)`

**인수**

* *h1*: 첫 번째 해시 값을 나타내는 긴 값입니다.
* *h2*: 두 번째 해시 값을 나타내는 긴 값입니다.
* *hN*: Nth 해시 값을 나타내는 긴 값입니다.

**반환**

지정된 스칼라의 결합된 해시 값입니다.

**예**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|결합|
|---|---|---|---|---|
|안녕하세요.|World|753694413698530628|1846988464401551951|-1440138333540407281|