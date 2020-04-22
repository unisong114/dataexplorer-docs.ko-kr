---
title: 고유 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 고유한 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030374"
---
# <a name="distinct-operator"></a>distinct 연산자

입력 테이블의 제공된 열의 고유한 조합이 있는 테이블을 생성합니다. 

```kusto
T | distinct Column1, Column2
```

입력 테이블의 모든 열이 고유한 조합으로 테이블을 생성합니다.

```kusto
T | distinct *
```

**예제**

과일과 가격의 뚜렷한 조합을 보여줍니다.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="별개":::

**참고 사항**

* 이와 달리 `summarize by ...`연산자는 별표`*`()를 그룹 키로 제공하여 넓은 테이블에 사용하기 쉽게 합니다. `distinct`
* 키별 그룹이 카디널리티가 높은 `summarize by ...` 경우 [셔플 전략을](shufflequery.md) 사용하는 것이 유용할 수 있습니다.