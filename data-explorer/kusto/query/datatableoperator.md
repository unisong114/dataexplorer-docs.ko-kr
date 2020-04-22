---
title: 데이터 테이블 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 테이블 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765388"
---
# <a name="datatable-operator"></a>datatable 연산자

쿼리 자체에 스키마와 값이 정의된 테이블을 반환합니다.

이 연산자에는 파이프라인 입력이 없습니다.

**구문**

`datatable``(` *열 이름* `:` 열`,` *유형* [...] `)` *ScalarValue* `,` *ScalarValue* 스칼라 밸류 [스칼라 밸류 ...] `[``]`

**인수**

::: zone pivot="azuredataexplorer"

* *열 이름*, *열 유형*: 테이블의 스키마를 정의합니다. 사용되는 구문은 테이블을 정의할 때 사용되는 구문과 정확히 [동일합니다(.create table](../management/create-table-command.md)참조).
* *ScalarValue*: 테이블에 삽입할 상수 스칼라 값입니다. 값 수는 테이블의 열 중 정수 배수여야 하며 *n'th*값에는 *n* % *NumColumns*열에 해당하는 형식이 있어야 합니다.

::: zone-end

::: zone pivot="azuremonitor"

* *열 이름*, *열 유형*: 테이블의 스키마를 정의합니다.
* *ScalarValue*: 테이블에 삽입할 상수 스칼라 값입니다. 값 수는 테이블의 열 중 정수 배수여야 하며 *n'th*값에는 *n* % *NumColumns*열에 해당하는 형식이 있어야 합니다.

::: zone-end

**반환**

이 연산자는 지정된 스키마 및 데이터의 데이터 테이블을 반환합니다.

**예제**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
