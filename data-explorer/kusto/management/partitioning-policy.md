---
title: 데이터 분할 정책 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663961"
---
# <a name="data-partitioning-policy-management"></a>데이터 분할 정책 관리

데이터 분할 정책은 여기에 자세히 [설명되어 있습니다.](../management/partitioningpolicy.md)

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy partitioning
```

`.show` 이 명령은 테이블에 적용되는 분할 정책을 표시합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 유형
|---|---|---|---|---
|데이터 분할 | 테이블 이름 | 정책 개체의 JSON 직렬화 | null | 테이블

## <a name="alter-and-alter-merge-policy"></a>정책 변경 및 변경

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

이 `.alter` 명령을 사용하면 테이블에 적용되는 분할 정책을 변경할 수 있습니다.

명령에는 [DatabaseAdmin](access-control/role-based-authorization.md) 권한이 필요합니다.

정책 변경사항이 적용되기까지 최대 1시간이 소요될 수 있습니다.

### <a name="examples"></a>예

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책의 모든 속성을 명시적으로 설정

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책의 특정 속성 설정

정책의 값을 `EffectiveDateTime` 다른 값으로 설정하려면 다음 명령을 사용합니다.

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>정책 삭제

```kusto
.delete table [table_name] policy partitioning
```

`.delete` 명령은 지정된 테이블의 분할 정책을 삭제합니다.
