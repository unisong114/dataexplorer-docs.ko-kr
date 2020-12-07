---
title: distinct 연산자 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 distinct 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513168"
---
# <a name="distinct-operator"></a>distinct 연산자

입력 테이블에서 제공된 열의 고유한 조합으로 테이블을 생성합니다. 

```kusto
T | distinct Column1, Column2
```

입력 테이블에 있는 모든 열의 고유한 조합으로 테이블을 생성합니다.

```kusto
T | distinct *
```

## <a name="example"></a>예제

과일과 가격의 고유한 조합을 보여줍니다.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="두 개의 테이블. 하나에는 공급자, 과일 유형 및 가격이 있으며 일부 과일 가격 조합이 반복됩니다. 두 번째 테이블에는 고유한 조합만 나열됩니다.":::

**참고**

* `summarize by ...`와 달리 `distinct` 연산자는 별표(`*`)를 그룹 키로 제공하여 넓은 테이블에 보다 쉽게 사용할 수 있도록 지원합니다.
* 키별 그룹화의 카디널리티가 높으면 [순서 섞기 전략](shufflequery.md)을 통해 `summarize by ...`를 사용하는 것이 유용할 수 있습니다.