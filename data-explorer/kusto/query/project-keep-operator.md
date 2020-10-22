---
title: project-keep operator-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 유지 운영자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356725"
---
# <a name="project-keep-operator"></a>project-keep 연산자

입력에서 출력에 유지할 열을 선택 합니다.

```kusto
T | project-keep price, quantity, zz*
```

결과의 열 순서는 테이블에서 원래 순서로 결정 됩니다. 인수로 지정 된 열만 유지 됩니다. 다른 열은 결과에서 제외 됩니다. [`project`](projectoperator.md) 항목을 참조하세요.

## <a name="syntax"></a>구문

*T* `| project-keep` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 입력 테이블
* *Columnnameorpattern:* 출력에 유지 되는 열 또는 열 와일드 카드 패턴의 이름입니다.

## <a name="returns"></a>반환

인수로 이름이 지정 된 열이 있는 테이블입니다. 입력 테이블과 동일한 수의 행을 포함 합니다.

> [!TIP]
>* 열 이름을 바꾸려면를 사용 [`project-rename`](projectrenameoperator.md) 합니다.
>* 열을 다시 정렬 하려면를 사용 [`project-reorder`](projectreorderoperator.md) 합니다.
>* `project-keep`원래 테이블에 있거나 쿼리의 일부로 계산 된 모든 열을 사용할 수 있습니다.

## <a name="example"></a>예제

입력 테이블 `T`에는 `long` 형식의 열로 `A`, `B`, `C`의 3개가 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>참고 항목

출력에서 제외할 입력의 열을 선택 하려면 [프로젝트](projectawayoperator.md)를 사용 합니다.
