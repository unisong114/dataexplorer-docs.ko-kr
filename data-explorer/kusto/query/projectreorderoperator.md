---
title: 프로젝트 순서 변경 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 순서 변경 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 74acab0dc4f0fbdaf7c77e609db3e41f875f2cad
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373145"
---
# <a name="project-reorder-operator"></a>project-reorder 연산자

결과 출력의 열 순서를 다시 정렬 합니다.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**구문**

*T* `| project-reorder` *columnnameorpattern* [ `asc` | `desc` ] [ `,` ...]

**인수**

* *T*: 입력 테이블입니다.
* *Columnnameorpattern:* 출력에 추가 된 열 또는 열 와일드 카드 패턴의 이름입니다.
* 와일드 카드 패턴의 경우 `asc` : `desc` 열 이름을 오름차순 이나 내림차순으로 지정 하거나 순서를 지정 합니다. `asc`또는이 `desc` 지정 되지 않은 경우 원본 테이블에 표시 되는 일치 하는 열에 의해 순서가 결정 됩니다.

**반환**

연산자 인수에 지정 된 순서로 열을 포함 하는 테이블입니다. `project-reorder`는 테이블에서 열 이름을 바꾸거나 제거 하지 않으므로 원본 테이블에 있던 모든 열이 결과 테이블에 표시 됩니다.

**참고 사항**

- 모호한 *Columnnameorpattern* 일치에서 열이 패턴과 일치 하는 첫 번째 위치에 표시 됩니다.
- 에 대 한 열 지정 `project-reorder` 은 선택 사항입니다. 명시적으로 지정 되지 않은 열은 출력 테이블의 마지막 열로 표시 됩니다.

* [`project-away`](projectawayoperator.md)를 사용 하 여 열을 제거 합니다.
* [`project-rename`](projectrenameoperator.md)열 이름을 바꾸려면를 사용 합니다.


**예**

세 개의 열 (a, b, c)이 있는 테이블을 다시 정렬 하 여 두 번째 열 (b)이 먼저 표시 되도록 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

로 시작 하는 열이 다른 열 앞에 표시 되도록 테이블의 열을 다시 정렬 `a` 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|00|a2|나타나는지|b|
|---|---|---|---|
|00|a2|나타나는지|b|
