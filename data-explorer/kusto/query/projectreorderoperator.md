---
title: 프로젝트 재정렬 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 재정렬 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf56a69891f83aaabc12dbbcd8f758ecb963b493
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510849"
---
# <a name="project-reorder-operator"></a>project-reorder 연산자

결과 출력에서 열을 재정렬합니다.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**구문**

*T* `| project-reorder` *열NameOrPattern* [`asc`|`desc`[ [ [`,`

**인수**

* *T*: 입력 테이블입니다.
* *열이름Or패턴:* 출력에 추가된 열 또는 열 와일드카드 패턴의 이름입니다.
* 와일드카드 패턴의 경우: `asc` `desc` 오름차순 또는 내림차순으로 이름을 사용하여 열을 지정하거나 주문합니다. 지정되지 않았거나 지정되지 않은 경우 `asc` 순서는 원본 테이블에 나타나는 일치 하는 열에 의해 결정 됩니다. `desc`

**반환**

연산자 인수에 의해 지정된 순서로 열을 포함하는 테이블입니다. `project-reorder`테이블에서 열의 이름을 바꾸거나 제거하지 않으므로 원본 테이블에 있는 모든 열이 결과 테이블에 나타납니다.

**참고 사항**

- 모호한 *ColumnNameOrPattern* 일치에서 열은 패턴과 일치하는 첫 번째 위치에 나타납니다.
- 에 `project-reorder` 대한 열을 지정하는 것은 선택 사항입니다. 명시적으로 지정되지 않은 열은 출력 테이블의 마지막 열로 나타납니다.

* 열을 [`project-away`](projectawayoperator.md) 제거하는 데 사용합니다.
* 열 [`project-rename`](projectrenameoperator.md) 이름을 바꿀 때 사용합니다.


**예**

세 개의 열(a, b, c)으로 테이블을 순서를 바맺어 두 번째 열(b)이 먼저 나타납니다.

```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

테이블의 열을 순서를 재정렬하여 다른 `a` 열 앞에 시작하는 열이 표시되도록 합니다.

```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|b|
|---|---|---|---|
|a1|a2|a3|b|