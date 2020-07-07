---
title: 분할 정책 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 분할 정책 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967471"
---
# <a name="sharding-policy-command"></a>분할 정책 명령

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`정책 데이터베이스 또는 테이블에 대 한 분할 정책을 표시 합니다. 지정 된 이름이 ' * ' 인 경우 지정 된 엔터티 유형 (데이터베이스 또는 테이블)의 모든 정책을 표시 합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 유형
|---|---|---|---|---
|ExtentsShardingPolicy | 데이터베이스/테이블 이름 | 정책을 나타내는 json 형식 문자열입니다. | 테이블 목록 (데이터베이스의 경우)|데이터베이스/테이블

## <a name="alter-policy"></a>정책 변경

### <a name="examples"></a>예

다음 예에서는 데이터베이스 또는 테이블을 정규화 된 이름으로 지정 하 여 엔터티에 대 한 업데이트 된 익스텐트 분할 정책을 출력으로 반환 합니다.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책에 대 한 모든 속성을 명시적으로 설정

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>데이터베이스 수준에서 정책에 대 한 모든 속성을 명시적으로 설정

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>데이터베이스 수준에서 *기본* 분할 정책 설정

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>데이터베이스 수준에서 정책의 단일 속성 변경 

다른 모든 속성은 그대로 유지 합니다.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>테이블 수준에서 정책의 단일 속성 변경

다른 모든 속성을 그대로 유지

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>정책 삭제

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

명령은 지정 된 엔터티에 대 한 현재 분할 정책을 삭제 합니다.