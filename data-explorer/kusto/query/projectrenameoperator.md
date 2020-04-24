---
title: 프로젝트 이름 바꾸기 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Data Explorer에서 프로젝트 이름 변경 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510866"
---
# <a name="project-rename-operator"></a>project-rename 연산자

결과 출력의 열을 다시 바꿉니다.

```kusto
T | project-rename new_column_name = column_name
```

**구문**

*T* `| project-rename` *NewColumnName* = *기존열 이름* [...]`,`

**인수**

* *T*: 입력 테이블입니다.
* *새 열 이름:* 열의 새 이름입니다. 
* *기존열 이름:* 열의 기존 이름입니다. 

**반환**

열의 이름이 바뀌는 기존 테이블과 동일한 순서로 열이 있는 테이블입니다.


**예**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|