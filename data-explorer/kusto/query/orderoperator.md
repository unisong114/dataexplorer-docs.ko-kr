---
title: 주문 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 주문 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 35fa0c29bf00a8696d4e9d08d73c30bba6171ea1
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264718"
---
# <a name="order-operator"></a>order 연산자 

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Order 연산자는 sort 연산자에 대 한 별칭입니다. 자세한 내용은 [sort 연산자](sortoperator.md) 를 참조 하세요.

**구문**

*T* `| order by` *열* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

**인수**

* *T*: 정렬할 테이블 입력입니다.
* *column*: 정렬 기준이 되는 *T* 의 열입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc`내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.
* `nulls first`(order에 대 한 기본값 `asc` )은 null 값을 시작 및 `nulls last` (order의 기본값 `desc` )은 끝에 null 값을 삽입 합니다.

