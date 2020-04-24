---
title: 연산자로 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 연산자로 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518431"
---
# <a name="as-operator"></a>as 연산자

연산자의 입력 테이블 형식 식에 이름을 바인딩하므로 쿼리가 [쿼리를](letstatement.md)끊고 let 문을 통해 이름을 바인딩하지 않고 여러 번 테이블 형식 식의 값을 참조할 수 있습니다.

**구문**

*T* `|` T `as` `hint.materialized` [ `=` *Name* 이름 `true`

**인수**

* *T*: 표식 식입니다.
* *이름*: 테이블 형식식의 임시 이름입니다.
* `hint.materialized`: 테이블 `true`형식 식의 값이 [materialize()](./materializefunction.md) 함수 호출에 의해 래핑된 것처럼 구체화됩니다.

**참고 사항**

* 제공된 `as` 이름은 [공용 구조의](./unionoperator.md) `withsource=` 열, [찾기](./findoperator.md) `source_` 열 및 [검색](./searchoperator.md) `$table` 열에 사용됩니다.

* [조인의](./joinoperator.md)외부 테이블 형식 입력 () 연산자`$left`사용 으로 명명 된 테이블 형식 표현식은 조인의 테이블 내부 입력 ()에서도`$right`사용할 수 있습니다.

**예**

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