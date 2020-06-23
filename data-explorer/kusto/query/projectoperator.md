---
title: Project operator-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 운영자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ead8fabe755d5e3e200a767cb8b7518121b2ac
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128957"
---
# <a name="project-operator"></a>project 연산자

포함, 이름 바꾸기 또는 삭제할 열을 선택하고 새 계산된 열을 삽입합니다. 

결과의 열 순서는 인수 순서에 의해 지정됩니다. 인수에 지정 된 열만 결과에 포함 됩니다. 입력의 다른 열은 모두 삭제 됩니다.  ( `extend`참조)

```kusto
T | project cost=price*quantity, price
```

**구문**

*T* `| project` *ColumnName* [ `=` *식*] [ `,` ...]
  
또는
  
*T* `| project` [*columnname*  |  `(` *columnname*[ `,` ] `)` `=` ] *식* [ `,` ...]

**인수**

* *T*: 입력 테이블입니다.
* *ColumnName:* 출력에 표시할 열의 선택적 이름입니다. *식이*없는 경우 *ColumnName* 은 필수 이며 해당 이름의 열이 입력에 표시 되어야 합니다. 생략 하면 이름이 자동으로 생성 됩니다. *식이* 둘 이상의 열을 반환 하는 경우 열 이름 목록을 괄호로 지정할 수 있습니다. 이 경우 *식*의 출력 열에는 지정 된 이름이 지정 됩니다 .이 경우 모든 나머지 출력 열은 삭제 됩니다. 열 이름 목록을 지정 하지 않으면 생성 된 이름이 있는 모든 *식*의 출력 열이 출력에 추가 됩니다.
* *Expression:* 입력 열을 참조하는 선택적 스칼라 식입니다. *ColumnName* 을 생략 하지 않으면 필수 *식* 입니다.

    입력의 기존 열과 같은 이름을 가진 새 계산된 열을 반환하는 것이 올바릅니다.

**반환**

인수로 이름 지정된 열 및 입력 테이블만큼의 행을 가진 테이블입니다.

**예제**

다음 예제에서는 `project` 연산자를 사용하여 수행할 수 있는 여러 종류의 조작을 보여 줍니다. 입력 테이블 `T`에는 `int` 형식의 열로 `A`, `B`, `C`의 3개가 있습니다. 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) 는 여러 열을 반환 하는 함수의 예입니다.