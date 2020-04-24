---
title: 프로젝트 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de13c240180d00b82736a0dd35cb83c08639682f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510934"
---
# <a name="project-operator"></a>프로젝트 연산자

포함, 이름 바꾸기 또는 삭제할 열을 선택하고 새 계산된 열을 삽입합니다. 

결과의 열 순서는 인수 순서에 의해 지정됩니다. 인수에 지정된 열만 결과에 포함됩니다. 입력의 다른 열이 삭제됩니다.  ( `extend`참조)

```kusto
T | project cost=price*quantity, price
```

**구문**

*T* `| project` 열`=` *이름* [`,` *표현식*] [ [...]
  
또는
  
*T* `| project` [*열 이름* | `(``)` `=`*열 이름*[ ]`,`] [ *표현식* [`,` ...]

**인수**

* *T*: 입력 테이블입니다.
* *열 이름:* 출력에 표시할 열의 선택적 이름입니다. *식이*없는 경우 *ColumnName은* 필수이며 해당 이름의 열이 입력에 나타나야 합니다. 생략하면 이름이 자동으로 생성됩니다. *Expression이* 두 개 이상의 열을 반환하는 경우 괄호 안에 열 이름 목록을 지정할 수 있습니다. 이 경우 *Expression의*출력 열에는 지정된 이름이 지정되어 출력 열의 나머지 모든 열이 있는 경우 삭제됩니다. 열 이름 목록을 지정하지 않으면 생성된 이름이 있는 모든 *Expression의*출력 열이 출력에 추가됩니다.
* *Expression:* 입력 열을 참조하는 선택적 스칼라 식입니다. *ColumnName을* 생략하지 않으면 *표현식이* 필수입니다.

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

[series_stats](series-statsfunction.md) 여러 열을 반환하는 함수의 예입니다.