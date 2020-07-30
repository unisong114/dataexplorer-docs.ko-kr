---
title: hash_combine ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_combine ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: f71a0d0cdfa4fe0ca8cdb84e65a271ee42bc7dc7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347615"
---
# <a name="hash_combine"></a>hash_combine()

둘 이상의 해시에 대 한 해시 값을 결합 합니다.

## <a name="syntax"></a>구문

`hash_combine(`*h1* `,` *h2* [ `,` *h3* ...]`)`

## <a name="arguments"></a>인수

* *h1*: 첫 번째 해시 값을 나타내는 Long 값입니다.
* *h2*: 두 번째 해시 값을 나타내는 Long 값입니다.
* *hN*: n 번째 해시 값을 나타내는 Long 값입니다.

## <a name="returns"></a>반환

지정 된 스칼라의 결합 된 해시 값입니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|전체|
|---|---|---|---|---|
|안녕하세요.|World|753694413698530628|1846988464401551951|-1440138333540407281|
