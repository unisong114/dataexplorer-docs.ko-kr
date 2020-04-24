---
title: . create table-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 만들기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108425"
---
# <a name="create-table"></a>.create table

비어 있는 새 테이블을 만듭니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.create``table` *TableName* ([columnName: columnType], ...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` 설명서] [ `folder` FolderName] `)`] `=`

테이블이 이미 있는 경우 명령이 success를 반환 합니다.

**예제** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**반환 출력**

는 다음과 같은 JSON 형식의 테이블 스키마를 반환 합니다.

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 여러 테이블을 만들려면 [테이블 만들기](create-tables-command.md) 명령을 사용 하 여 클러스터의 성능 및 낮은 부하를 향상 합니다.

## <a name="create-merge-table"></a>. create-merge 테이블

새 테이블을 만들거나 기존 테이블을 확장 합니다. 

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다. 

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.create-merge``table` *TableName* ([columnName: columnType], ...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` 설명서] [ `folder` FolderName] `)`] `=`

테이블이 없는 경우는 "create table" 명령으로 정확 하 게 작동 합니다.

테이블이 T가 있는 경우 ". create-merge table T (<columns specification>)" 명령을 보내려면 다음을 수행 합니다.

* 이전에 T <columns specification> 에 존재 하지 않은의 모든 열이 t의 스키마 끝에 추가 됩니다.
* 에 <columns specification> 없는 t의 모든 열은 t에서 제거 되지 않습니다.
* T에는 <columns specification> 있지만 데이터 형식이 다른의 모든 열은 명령이 실패 합니다.
