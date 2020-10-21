---
title: 프로젝트 이름 바꾸기 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 프로젝트 이름 바꾸기 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83a4dba3f426189de615640ab48e1fa60f9e5537
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242202"
---
# <a name="project-rename-operator"></a>project-rename 연산자

결과 출력에서 열의 이름을 바꿉니다.

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>구문

*T* `| project-rename` *newcolumnname*  =  *existingcolumnname* [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 입력 테이블입니다.
* *Newcolumnname:* 열의 새 이름입니다. 
* *Existingcolumnname:* 열의 기존 이름입니다. 

## <a name="returns"></a>반환

열 이름이 인 기존 테이블과 동일한 순서로 열이 있는 테이블입니다.


## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
