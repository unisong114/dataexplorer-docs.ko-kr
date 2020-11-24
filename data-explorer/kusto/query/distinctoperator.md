---
title: distinct 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 distinct 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513168"
---
# <a name="distinct-operator"></a>distinct 연산자

입력 테이블에서 제공 된 열의 고유한 조합을 사용 하 여 테이블을 생성 합니다. 

```kusto
T | distinct Column1, Column2
```

입력 테이블에 있는 모든 열의 고유한 조합을 사용 하 여 테이블을 생성 합니다.

```kusto
T | distinct *
```

## <a name="example"></a>예제

과일과 가격의 고유한 조합을 보여 줍니다.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="두 테이블 하나에는 공급 업체, 과일 유형 및 가격이 있으며 일부 과일 가격 조합은 반복 됩니다. 두 번째 테이블에는 고유한 조합만 나열 됩니다.":::

**참고**

* 와 달리 `summarize by ...` `distinct` 연산자는 별표 ()를 그룹 키로 제공 하 여 `*` 넓은 테이블에 보다 쉽게 사용할 수 있도록 지원 합니다.
* Group by 키가 high 카디널리티 인 경우을 순서 대로 사용 하 여를 사용 하는 `summarize by ...` 것이 유용할 수 있습니다. [shuffle strategy](shufflequery.md)