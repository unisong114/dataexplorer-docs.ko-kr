---
title: 병합 정책 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 병합 정책 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9ef6f2cd2359e35e90c3903738adf82728e9da40
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867072"
---
# <a name="merge-policy-management"></a>병합 정책 관리

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

데이터베이스 또는 테이블에 대 한 현재 병합 정책을 표시 합니다.
지정 된 이름이 ' * ' 인 경우 지정 된 엔터티 형식 (데이터베이스 또는 테이블)의 모든 정책을 표시 합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 형식
|---|---|---|---|---
|ExtentsMergePolicy | 데이터베이스/테이블 이름 | 정책을 나타내는 JSON 형식 문자열입니다. | 테이블 목록 (데이터베이스의 경우)|데이터베이스 &#124; 테이블

## <a name="alter-policy"></a>정책 변경

### <a name="examples"></a>예

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. 테이블 수준에서 정책에 대 한 모든 속성을 명시적으로 설정 합니다.

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. 데이터베이스 수준에서 정책의 모든 속성을 명시적으로 설정 합니다.

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. 데이터베이스 수준에서 *기본* 병합 정책을 설정 합니다.

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. 데이터베이스 수준에서 정책의 단일 속성을 변경 하 고 다른 모든 속성을 그대로 유지 합니다.

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. 테이블 수준에서 정책의 단일 속성을 변경 하 여 다른 모든 속성을 그대로 유지 합니다.

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

위의 모든 항목에서는 엔터티 (정규화 된 이름으로 지정 된 데이터베이스 또는 테이블)에 대 한 업데이트 된 익스텐트 병합 정책을 출력으로 반환 합니다.

정책에 대 한 변경 내용을 적용 하는 데 최대 1 시간이 걸릴 수 있습니다.

## <a name="delete-policy-of-merge"></a>병합 정책 삭제

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

명령은 지정 된 엔터티에 대 한 현재 병합 정책을 삭제 합니다.
