---
title: Null 값 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 Null 값에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765948"
---
# <a name="null-values"></a>Null 값

Kusto의 모든 스칼라 데이터 형식에는 누락된 값을 나타내는 특수 값이 있습니다.
이 값을 **null 값**또는 단순히 null 값이라고 **합니다.**

## <a name="null-literals"></a>널 리터럴

스칼라 형식의 `T` null 값은 null 리터럴로 `T(null)`쿼리 언어로 표시됩니다.
따라서 다음 null로 가득 찬 단일 행을 반환합니다.

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 현재 형식은 `string` null 값을 지원하지 않습니다.

## <a name="comparing-null-to-something"></a>null을 무언가와 비교

null 값은 자체를 포함하여 데이터 형식의 다른 값과 동일하게 비교되지 않습니다. (즉, `null == null` 거짓입니다.) 일부 값이 null 값인지 확인하려면 [isnull()](../isnullfunction.md) 함수와 [isnotnull() 함수를](../isnotnullfunction.md) 사용합니다.

## <a name="binary-operations-on-null"></a>null의 이진 작업

일반적으로 null은 이진 연산자 주위에 "끈적끈적한" 방식으로 행동합니다. null 값과 다른 값(다른 null 값 포함) 간의 이진 연산은 null 값을 생성합니다.

## <a name="data-ingestion-and-null-values"></a>데이터 수집 및 null 값

대부분의 데이터 형식의 경우 데이터 원본에서 누락된 값은 해당 테이블 셀에서 null 값을 생성합니다. 누락된 값이 빈 문자열을 `string` 생성하는 형식 및 CSV와 같은 인식의 열에 대한 예외입니다.
예를 들어, 다음과 같은 경우: 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

그렇다면

|a     |b     |이스널(a)|[a]|스트렌 (a)|이스널(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Kusto.Explorer에서 위의 쿼리를 실행하면 `true` 모든 값이 `1`로 분리되고 모든 `false` 값이 로 `0`표시됩니다.

::: zone-end

* Kusto는 테이블의 열에 null 값이 있는 것을 제한하는 방법을 제공하지 않습니다(즉, SQL의 `NOT NULL` 제약 조건과 동등한 것은 없습니다).
