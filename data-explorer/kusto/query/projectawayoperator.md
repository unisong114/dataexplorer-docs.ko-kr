---
title: 프로젝트 자리 비움 운영자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 자리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2938019237c882891af8ff86f4d33de3605a9063
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356523"
---
# <a name="project-away-operator"></a>project-away 연산자

출력에서 제외할 입력의 열을 선택 합니다.

```kusto
T | project-away price, quantity, zz*
```

결과의 열 순서는 테이블에서 원래 순서로 결정 됩니다. 인수로 지정 된 열만 삭제 됩니다. 다른 열은 결과에 포함 됩니다. ( `project`참조)

## <a name="syntax"></a>구문

*T* `| project-away` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 입력 테이블
* *Columnnameorpattern:* 출력에서 제거할 열 또는 열 와일드 카드 패턴의 이름입니다.

## <a name="returns"></a>반환

인수로 이름이 지정 되지 않은 열이 있는 테이블입니다. 입력 테이블과 동일한 수의 행을 포함 합니다.

> [!TIP]
>
> * 열 이름을 바꾸려면를 사용 [`project-rename`](projectrenameoperator.md) 합니다.
> * 열을 다시 정렬 하려면를 사용 [`project-reorder`](projectreorderoperator.md) 합니다.
> * `project-away`원래 테이블에 있거나 쿼리의 일부로 계산 된 모든 열을 사용할 수 있습니다.

## <a name="examples"></a>예

입력 테이블 `T`에는 `long` 형식의 열로 `A`, `B`, `C`의 3개가 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

' A '로 시작 하는 열을 제거 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a2='a2', b = 'b', a3='a3', a1='a1'
| project-away a*
```

|b|
|---|
|b|

## <a name="see-also"></a>참고 항목

입력에서 출력에 유지할 열을 선택 하려면 [프로젝트-유지](project-keep-operator.md)를 사용 합니다.
