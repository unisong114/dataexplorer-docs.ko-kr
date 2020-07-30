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
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347598"
---
# <a name="hash_many"></a>hash_many()

여러 값의 결합 된 해시 값을 반환 합니다.

## <a name="syntax"></a>구문

`hash_many(`*s1* `,` *s2* [ `,` *s3* ...]`)`

## <a name="arguments"></a>인수

* *s1*, *s2*, ..., *sN*: 함께 해시 되는 입력 값입니다.

## <a name="returns"></a>반환

지정 된 스칼라의 결합 된 해시 값입니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|전체|
|---|---|---|
|안녕하세요.|World|-1440138333540407281|
