---
title: 정책 관리 병합 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 병합 정책 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664018"
---
# <a name="merge-policy-management"></a>정책 관리 병합

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

데이터베이스 또는 테이블에 대한 현재 병합 정책을 표시합니다.
지정된 이름이 '*'인 경우 지정된 엔터티 유형(데이터베이스 또는 테이블)의 모든 정책을 표시합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 형식
|---|---|---|---|---
|익스텐트병합 정책 | 데이터베이스 / 테이블 이름 | 정책을 나타내는 JSON 형식의 문자열 | 테이블 목록(데이터베이스용)|데이터베이스 &#124; 테이블

## <a name="alter-policy"></a>정책 변경

### <a name="examples"></a>예

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. 테이블 수준에서 정책의 모든 속성을 명시적으로 설정합니다.

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. 데이터베이스 수준에서 정책의 모든 속성을 명시적으로 설정합니다.

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. 데이터베이스 수준에서 *기본* 병합 정책 설정:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. 데이터베이스 수준에서 정책의 단일 속성을 변경하여 다른 모든 속성을 있는 상태로 유지합니다.

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. 테이블 수준에서 정책의 단일 속성을 변경하여 다른 모든 속성을 있는 상태로 유지합니다.

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

위의 모든 엔터티에 대 한 업데이트 된 익스텐스 병합 정책을 반환 (데이터베이스 또는 테이블 정규화 된 이름으로 지정) 출력으로.

정책 변경사항이 적용되기까지 최대 1시간이 소요될 수 있습니다.

## <a name="delete-policy-of-merge"></a>병합 정책 삭제

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

명령은 지정된 엔터티에 대한 현재 병합 정책을 삭제합니다.
