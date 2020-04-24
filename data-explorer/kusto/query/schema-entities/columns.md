---
title: 열 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 열에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 17406eb94f8e29f4b8ab87653ab41df737fa15ef
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509489"
---
# <a name="columns"></a>열

Kusto의 모든 [테이블과](tables.md) 모든 테이블 형식 데이터 스트림은 열과 행의 직사각형 격자입니다. 테이블의 모든 열에는 이름과 특정 [스칼라 데이터 형식이](../scalar-data-types/index.md)있습니다. 테이블 또는 테이블 형식 데이터 스트림의 열이 정렬되므로 열에는 테이블의 열 컬렉션에 특정 위치가 있습니다.

**참고 사항**  

* 열 이름은 대/소문자를 구분합니다.
* 열 이름은 엔터티 [이름에](./entity-names.md)대한 규칙을 따릅니다.
* 테이블당 최대 열 제한은 10,000입니다.

쿼리에서 열은 일반적으로 이름으로만 참조됩니다. 식에만 표시될 수 있으며 식이 나타나는 쿼리 연산자는 테이블 또는 테이블 형식 데이터 스트림을 결정하므로 열의 이름을 더 이상 범위를 지정할 필요가 없습니다. 예를 들어 다음 쿼리에는 명명되지 않은 테이블 형식 데이터 스트림이 있습니다(단일 열이 `c`있는 [datatable 연산자,](../datatableoperator.md) . 그런 다음 테이블 형식 데이터 스트림이 해당 열값에 대한 조건부로 필터링되어 동일한 열이 있지만 행이 적은 새 명명되지 않은 테이블 형식 데이터 스트림이 생성됩니다. 그런 다음 [as 연산자는](../asoperator.md) 테이블 형식 의 데이터 스트림의 이름을 지정하고 해당 값은 쿼리 결과로 반환됩니다.
특히 해당 컨테이너를 `c` 참조할 필요 없이 열이 이름으로 참조되는 방법에 유의하십시오(실제로 해당 컨테이너에는 이름이 없음).

```kusto
datatable (c:int) [int(-1), 0, 1, 2, 3]
| where c*c >= 2
| as Result
```

> 관계형 데이터베이스 세계에서 흔히 볼 수 있듯이 행을 **레코드라고도** 하며 열은 **특성이라고도 합니다.**

열 관리에 대한 자세한 내용은 열 관리 에서 찾을 수 [있습니다.](../../management/columns.md)