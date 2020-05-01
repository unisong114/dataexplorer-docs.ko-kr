---
title: . 테이블 이름 바꾸기 및 테이블 이름 바꾸기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 테이블 이름 바꾸기 및 Azure 데이터 탐색기에서 테이블 이름 바꾸기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617581"
---
# <a name="rename-table-and-rename-tables"></a>. 테이블 이름 바꾸기 및 테이블 이름 바꾸기

기존 테이블의 이름을 변경 합니다.

이 `.rename tables` 명령은 데이터베이스에 있는 여러 테이블의 이름을 단일 트랜잭션으로 변경 합니다.

[데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.rename``table` *OldName* OldName `to` *NewName*

`.rename``tables` *NewName*NewName = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName* 는 기존 테이블의 이름입니다. *OldName* 가 지정 되지 않은 경우 (이 경우 rename 명령의이 부분이 무시 됨),가 지정 되지 않은 `ifexists` 경우 오류가 발생 하 고 전체 명령이 실패 합니다 (영향을 주지 않음).
> * *NewName* 은 *OldName*라고 하는 기존 테이블의 새 이름입니다.
> * 을 `ifexists` 지정 하면 존재 하지 않는 테이블의 이름을 바꾸는 것을 무시 하도록 명령의 동작을 수정 합니다.

**주의**

이 명령은 범위 내 에서만 데이터베이스의 테이블에 대해 작동 합니다.
테이블 이름은 클러스터 또는 데이터베이스 이름으로 한정할 수 없습니다.

이 명령은 새 테이블을 만들지 않으며 기존 테이블도 제거 하지 않습니다.
명령에서 설명 하는 변환은 데이터베이스의 테이블 수가 변경 되지 않도록 해야 합니다.

**이** 명령은 위의 규칙을 준수 하는 한 테이블 이름 또는 더 복잡 한 순열의 스와핑을 지원 합니다. 예를 들어 여러 준비 테이블에 데이터를 수집한 다음 단일 트랜잭션으로 기존 테이블로 바꿉니다.

**예제**

`A` `B`, `C`, 및 `A_TEMP`테이블이 포함 된 데이터베이스를 상상해 보세요.
`A` 다음 명령은와 `A_TEMP` 를 교환 하 여 `A_TEMP` 테이블을 호출 `A`하 고 다른 방법으로 이름을로 `B` `NEWB`바꾸고 그대로 유지 `C` 합니다. 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

다음과 같은 일련의 명령이 있습니다.
1. 새 임시 테이블을 만듭니다.
1. 기존 또는 기존이 아닌 테이블을 새 테이블로 바꿉니다.

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
