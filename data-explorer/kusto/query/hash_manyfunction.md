---
title: hash_many ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_many ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226672"
---
# <a name="hash_many"></a>hash_many()

여러 값의 결합 된 해시 값을 반환 합니다.

**구문**

`hash_many(`*s1* `,` *s2* [ `,` *s3* ...]`)`

**인수**

* *s1*, *s2*, ..., *sN*: 함께 해시 되는 입력 값입니다.

**반환**

지정 된 스칼라의 결합 된 해시 값입니다.

**예**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|전체|
|---|---|---|
|안녕하세요.|World|-1440138333540407281|
