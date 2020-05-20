---
title: bag_unpack 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_unpack 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: fd8b0968d90f1c5239cae80c3be9c2a32d0603d6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225499"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 플러그 인

`bag_unpack`플러그 인은 `dynamic` 각 속성 모음 최상위 슬롯을 열로 처리 하 여 형식의 단일 열을 압축 합니다.

    T | evaluate bag_unpack(col1)

**구문**

*T* `|` `evaluate` `bag_unpack(` *열* `,` [ *outputcolumnprefix* ]`)`

**인수**

* *T*: 열 *열* 을 압축을 풀 테이블 형식 입력입니다.
* *Column*: 압축을 풀 *T* 의 열입니다. 반드시 `dynamic` 형식이어야 합니다.
* *Outputcolumnprefix*: 플러그 인에서 생성 하는 모든 열에 추가할 일반적인 접두사입니다.
  (선택 사항)

**반환**

`bag_unpack`플러그 인은 레코드를 테이블 형식 입력 (*T*)으로 포함 하는 테이블을 반환 합니다. 테이블의 스키마는 다음과 같은 수정 내용이 있는 테이블 형식 입력의 스키마와 동일 합니다.

* 지정한 입력 열 (*열*)이 제거 됩니다.

* 이 스키마는 *T*의 최상위 속성 모음 값에서 고유한 슬롯 수 만큼의 열로 확장 됩니다. 각 열의 이름은 각 슬롯의 이름에 해당 하며 필요에 따라 *Outputcolumnprefix*접두사가 붙습니다. 해당 형식은 슬롯의 형식 (동일한 슬롯의 모든 값이 동일한 형식일 경우) 또는 `dynamic` (값이 형식이 다른 경우)입니다.

**참고 사항**

플러그 인의 출력 스키마는 데이터 값에 따라 달라 지 며 데이터 자체로 "예측할 수 없음"으로 설정 됩니다. 따라서 데이터 입력이 다른 플러그 인을 여러 번 실행 하면 다른 출력 스키마가 생성 될 수 있습니다.

플러그 인에 대 한 입력 데이터는 출력 스키마가 테이블 형식 스키마에 대 한 모든 규칙을 준수 해야 합니다. 특히 다음 사항에 주의하십시오.

1. 출력 열 이름은 같은 이름으로 두 개의 열을 생성 하므로 압축을 푸는 (*열*) 열이 아닌 경우 테이블 *형식 입력의* 기존 열과 같을 수 없습니다.

2. 모든 슬롯 이름에 대해 *Outputcolumnprefix*접두사가 유효한 엔터티 이름 이어야 하 고 [식별자 명명 규칙](./schema-entities/entity-names.md#identifier-naming-rules)을 준수 해야 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|Smitha|30 |
