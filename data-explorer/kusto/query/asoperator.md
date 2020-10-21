---
title: as operator-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 as 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15dcf79938f4b83f18055b6f59a9b70998ab6049
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252818"
---
# <a name="as-operator"></a>as 연산자

연산자의 입력 테이블 형식 식에 이름을 바인딩하여 쿼리를 중단 하 고 [let 문을](letstatement.md)통해 이름을 바인딩하지 않고 테이블 형식 식의 값을 여러 번 참조할 수 있습니다.

## <a name="syntax"></a>구문

*T* `|` `as` [ `hint.materialized` `=` `true` ] *이름*

## <a name="arguments"></a>인수

* *T*: 테이블 형식 식입니다.
* *이름*: 테이블 형식 식의 임시 이름입니다.
* `hint.materialized`:로 설정 하면 `true` 테이블 형식 식의 값이 [구체화 ()](./materializefunction.md) 함수 호출로 래핑된 것 처럼 구체화 됩니다.

> [!NOTE]
> * 에서 지정 하는 이름은 `as` `withsource=` [union](./unionoperator.md)의 열, `source_` [find](./findoperator.md)열 및 `$table` [search](./searchoperator.md)의 열에서 사용 됩니다.
> * [조인의 외부](./joinoperator.md)테이블 형식 입력 ()에서 연산자를 사용 하 여 명명 된 테이블 형식 식은 `$left` 조인의 테이블 내부 입력 () 에서도 사용할 수 있습니다 `$right` .

## <a name="examples"></a>예

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```