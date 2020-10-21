---
title: 주문 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 주문 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f552143be8c02cece19030fc7b6f79d5a4bdf4a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241398"
---
# <a name="order-operator"></a>order 연산자 

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Order 연산자는 sort 연산자에 대 한 별칭입니다. 자세한 내용은 [sort 연산자](sortoperator.md) 를 참조 하세요.

## <a name="syntax"></a>구문

*T* `| order by` *열* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 정렬할 테이블 입력입니다.
* *column*: 정렬 기준이 되는 *T* 의 열입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc`내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.
* `nulls first` (order에 대 한 기본값 `asc` )은 null 값을 시작 및 `nulls last` (order의 기본값 `desc` )은 끝에 null 값을 삽입 합니다.

