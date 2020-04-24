---
title: bag_unpack 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 bag_unpack 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 374ff3ca8101e24a53926a78a1b8781447f32dbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518227"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 플러그인

플러그인은 `bag_unpack` 각 속성 가방 최상위 `dynamic` 슬롯을 열로 처리하여 형식의 단일 열을 압축 해제합니다.

    T | evaluate bag_unpack(col1)

**구문**

*T* `|` T `evaluate` `,` *Column* 열 [ *출력열사전픽]* `bag_unpack(``)`

**인수**

* *T*: 열 *열을* 풀어야 하는 테이블 형식 입력입니다.
* *열*: 압축을 풀 *T의* 열입니다. 반드시 `dynamic` 형식이어야 합니다.
* *OutputColumnPrefix*: 플러그인에서 생성된 모든 열에 추가할 공통 접두사입니다.
  (선택 사항)

**반환**

플러그인은 `bag_unpack` 테이블*입력(T)만큼*많은 레코드가 있는 테이블을 반환합니다. 테이블의 스키마는 다음과 같은 수정 사항을 통해 테이블 형식 입력의 스키마와 동일합니다.

* 지정된 입력*열(열)이*제거됩니다.

* 스키마는 *T의*최상위 속성 bag 값에 고유한 슬롯이 있는 만큼 많은 열로 확장됩니다. 각 열의 이름은 각 슬롯의 이름에 해당하며, 선택적으로 *OutputColumnPrefix에*의해 접두사에 붙어 있습니다. 해당 형식은 슬롯 의 유형(동일한 슬롯의 모든 값이 동일한 형식인 `dynamic` 경우) 또는 (값이 형식이 다른 경우) 형식입니다.

**참고 사항**

플러그인의 출력 스키마는 데이터 값에 따라 달라지므로 데이터 자체로 "예측할 수 없는" 스키마가 됩니다. 따라서 다른 데이터 입력을 가진 플러그인의 여러 실행은 다른 출력 스키마를 생성할 수 있습니다.

출력 스키마가 테이블 형식 스키마에 대한 모든 규칙을 준수하도록 플러그인에 대한 입력 데이터가 있어야 합니다. 특히 다음 사항에 주의하십시오.

1. 출력 열 이름은 풀수 있는*열(Column)* 자체가 같지 않으면 테이블 입력 *T의* 기존 열과 같을 수 없습니다.

2. *OutputColumnPrefix에*의해 접두사에 의해 접두사때 모든 슬롯 이름은 유효한 엔터티 이름이어야 하며 [식별자 명명 규칙을](./schema-entities/entity-names.md#identifier-naming-rules)준수해야 합니다.

**예제**

```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|이름  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|스미다 (미국)|30 |