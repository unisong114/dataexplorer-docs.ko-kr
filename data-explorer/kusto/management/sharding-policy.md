---
title: 샤딩 정책 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 샤딩 정책 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520046"
---
# <a name="sharding-policy-management"></a>샤딩 정책 관리

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`정책은 데이터베이스 또는 테이블에 대한 샤딩 정책을 표시합니다. 지정된 이름이 '*'인 경우 지정된 엔터티 유형(데이터베이스 또는 테이블)의 모든 정책을 표시합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 유형
|---|---|---|---|---
|익스텐트샤딩정책 | 데이터베이스 / 테이블 이름 | 정책을 나타내는 json 형식 문자열 | 테이블 목록(데이터베이스용)|데이터베이스 / 테이블

## <a name="alter-policy"></a>정책 변경

### <a name="examples"></a>예

다음 예제에서는 데이터베이스 또는 테이블을 정규화된 이름으로 지정하여 엔터티에 대해 업데이트된 익스텐트 샤딩 정책을 출력으로 반환합니다.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책의 모든 속성을 명시적으로 설정

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>데이터베이스 수준에서 정책의 모든 속성을 명시적으로 설정

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>데이터베이스 수준에서 *기본* 샤딩 정책 설정

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>데이터베이스 수준에서 정책의 단일 속성 변경 

다른 모든 속성을 있는 상태로 유지합니다.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>테이블 수준에서 정책의 단일 속성 변경

다른 모든 속성을 있는 상태로 유지

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>정책 삭제

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

명령은 지정된 엔터티에 대한 현재 샤딩 정책을 삭제합니다.