---
title: Null 값-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Null 값을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c493431fcfa22ad0419659a5b6e036205f3bf299
ms.sourcegitcommit: 194453a8eb11c3ccb54c473e887c84cb8e91b939
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87473975"
---
# <a name="null-values"></a>Null 값

Kusto의 모든 스칼라 데이터 형식은 누락 된 값을 나타내는 특수 한 값을 가집니다.
이 값을 **null 값**또는 단순히 **null**값 이라고 합니다.

## <a name="null-literals"></a>Null 리터럴

스칼라 형식의 null 값은 `T` 쿼리 언어에서 null 리터럴로 표현 됩니다 `T(null)` .
따라서 다음은 null의 전체 단일 행을 반환 합니다.

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 현재 `string` 형식은 null 값을 지원 하지 않습니다.

## <a name="comparing-null-to-something"></a>Null과 항목 비교

Null 값은 자신을 포함 하 여 데이터 형식의 다른 값과 같지 않습니다. 즉,가 `null == null` false입니다. 일부 값이 null 값 인지 확인 하려면 [isnull ()](../isnullfunction.md) 함수와 [isnotnull ()](../isnotnullfunction.md) 함수를 사용 합니다.

## <a name="binary-operations-on-null"></a>Null에 대 한 이항 연산

일반적으로 null은 이항 연산자 주위에 "고정" 방식으로 작동 합니다. null 값과 다른 값 (다른 null 값 포함) 사이의 이항 연산은 null 값을 생성 합니다.

## <a name="data-ingestion-and-null-values"></a>데이터 수집 및 null 값

대부분의 데이터 형식에 대해 데이터 원본의 누락 값이 해당 테이블 셀에 null 값을 생성 합니다. 에 대 한 예외는 형식 `string` 및 CSV와 유사한 수집의 열입니다. 여기서 누락 값은 빈 문자열을 생성 합니다.
예를 들어, 다음과 같은 경우 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

그런 다음:

|a     |b     |isnull (a)|isempty (a)|strlen (a)|isnull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 위의 쿼리를 Kusto. 탐색기에서 실행 하면 모든 값이 `true` 로 표시 되 `1` 고 모든 `false` 값이로 표시 됩니다 `0` .

::: zone-end

* Kusto는 테이블의 열에 null 값이 포함 되지 않도록 제한 하는 방법을 제공 하지 않습니다. 즉, SQL의 제약 조건에 해당 하는 것은 없습니다 `NOT NULL` .
