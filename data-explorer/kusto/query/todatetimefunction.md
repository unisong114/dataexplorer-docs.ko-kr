---
title: system.xml.xmlconvert.todatetime ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 system.xml.xmlconvert.todatetime ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b9248ddc76a4893cf1edd353c0fbd036c9a1e7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250542"
---
# <a name="todatetime"></a>todatetime()

입력을 [datetime](./scalar-data-types/datetime.md) 스칼라로 변환 합니다.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>구문

`todatetime(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: [datetime](./scalar-data-types/datetime.md)으로 변환 되는 식입니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 됩니다.
그 결과는 null이 됩니다.
 
> [!NOTE]
> 가능 하면 [datetime ()을](./scalar-data-types/datetime.md) 사용 하는 것이 좋습니다.
