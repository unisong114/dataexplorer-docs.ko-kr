---
title: todatetime() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 todatetime()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506344"
---
# <a name="todatetime"></a>todatetime()

입력을 [날짜 시간](./scalar-data-types/datetime.md) 스칼라로 변환합니다.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**구문**

`todatetime(`*Expr*`)`

**인수**

* *예시*: [날짜 시간으로](./scalar-data-types/datetime.md)변환되는 표현식 . 

**반환**

전환에 성공하면 결과는 [날짜 시간](./scalar-data-types/datetime.md) 값이 됩니다.
변환에 성공하지 못하면 결과는 null이 됩니다.
 
*참고*: 가능하면 [datetime()를](./scalar-data-types/datetime.md) 사용하는 것을 선호합니다.