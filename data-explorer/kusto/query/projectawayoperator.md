---
title: 프로젝트 어웨이 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 어웨이 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510951"
---
# <a name="project-away-operator"></a>project-away 연산자

출력에서 제외할 입력의 열을 선택합니다.

```kusto
T | project-away price, quantity, zz*
```

결과의 열 순서는 테이블의 원래 순서에 따라 결정됩니다. 인수로 지정된 열만 삭제됩니다. 다른 열이 결과에 포함됩니다.  ( `project`참조)

**구문**

*T* `| project-away` *열이름OrPattern* [...]`,`

**인수**

* *T*: 입력 테이블
* *열이름Or패턴:* 출력에서 제거할 열 또는 열 와일드카드 패턴의 이름입니다.

**반환**

인수로 이름이 지정되지 않은 열이 있는 테이블입니다. 입력 테이블과 동일한 수의 행을 포함합니다.

**팁**

* 열이름을 바꾸려는 의도가 있는 경우 사용합니다. [`project-rename`](projectrenameoperator.md)
* 열 [`project-reorder`](projectreorderoperator.md) 순서를 다시 정렬하려는 경우 사용합니다.

* 원래 `project-away` 테이블에 있거나 쿼리의 일부로 계산된 열을 사용할 수 있습니다.


**예**

입력 테이블 `T`에는 `long` 형식의 열로 `A`, `B`, `C`의 3개가 있습니다.

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|b|
|---|---|
|1|2|

'a'로 시작하는 열을 제거합니다.

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

