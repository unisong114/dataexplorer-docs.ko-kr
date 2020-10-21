---
title: datatable 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 datatable 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6a5f12c947d62b7ec13ab6d9d6a4564881de08a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247855"
---
# <a name="datatable-operator"></a>datatable 연산자

스키마와 값이 쿼리 자체에 정의 되어 있는 테이블을 반환 합니다.

> [!NOTE]
> 이 연산자에는 파이프라인 입력이 없습니다.

## <a name="syntax"></a>구문

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *ScalarValue* [ `,` *ScalarValue* ...]`]`

## <a name="arguments"></a>인수

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: 이러한 인수는 테이블의 스키마를 정의 합니다. 이 인수는 테이블을 정의할 때 사용 되는 구문과 동일한 구문을 사용 합니다.
  자세한 내용은 [테이블 만들기](../management/create-table-command.md)를 참조 하세요.
* *ScalarValue*: 테이블에 삽입할 상수 스칼라 값입니다. 값의 개수는 테이블에 있는 열의 정수 배수 여야 합니다. *N*번째 값에는 열 *n*  %  *numcolumns*에 해당 하는 형식이 있어야 합니다.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: 이러한 인수는 테이블의 스키마를 정의 합니다.
* *ScalarValue*: 테이블에 삽입할 상수 스칼라 값입니다. 값의 개수는 테이블에 있는 열의 정수 배수 여야 합니다. *N*번째 값에는 열 *n*  %  *numcolumns*에 해당 하는 형식이 있어야 합니다.

::: zone-end

## <a name="returns"></a>반환

이 연산자는 지정 된 스키마 및 데이터의 데이터 테이블을 반환 합니다.

## <a name="example"></a>예제

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
