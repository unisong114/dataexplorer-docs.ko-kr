---
title: .name table 및 .rename 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Data Explorer에서 .rename 테이블 및 .rename 테이블의 이름을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520488"
---
# <a name="rename-table-and-rename-tables"></a>.name name table 및 .rename table

기존 테이블의 이름을 변경합니다.

명령은 `.rename tables` 데이터베이스의 여러 테이블 이름을 단일 트랜잭션으로 변경합니다.

[데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.rename``table` *이전 이름* `to` *새 이름*

`.rename``tables` *새 이름* = *이전 이름* [ [`ifexists`[ [ [ [...]`,`

> [!NOTE]
> * *OldName은* 기존 테이블의 이름입니다. *OldName이* 지정되지 않는 한(이 `ifexists` 경우 이름 바꾸기 명령의 이 부분은 무시됨) 기존 테이블의 이름을 지정하지 않으면 오류가 발생되고 전체 명령이 실패합니다(아무런 효과 없음).
> * *NewName은* *OldName*이라고 하는 데 사용 하는 기존 테이블의 새 이름입니다.
> * `ifexists` 지정하면 존재하지 않는 테이블의 이름 바꾸기 부분을 무시하도록 명령의 동작을 수정합니다.

**주의**

이 명령은 범위의 데이터베이스 테이블에서만 작동합니다.
테이블 이름은 클러스터 또는 데이터베이스 이름으로 한정할 수 없습니다.

이 명령은 새 테이블을 만들지 않으며 기존 테이블을 제거하지도 않습니다.
명령에 설명된 변환은 데이터베이스의 테이블 수가 변경되지 않도록 해야 합니다.

**이** 명령은 위의 규칙을 준수하는 한 테이블 이름 교환 또는 보다 복잡한 순열을 지원합니다. 예를 들어 데이터를 여러 스테이징 테이블로 수집한 다음 단일 트랜잭션의 기존 테이블과 교환합니다.

**예**

`A`다음 테이블이 `B` `C` `A_TEMP`있는 데이터베이스를 상상해 보십시오.
다음 `A` 명령은 스왑되고 `A_TEMP` (테이블이 `A_TEMP` 이제 호출되고 `A`다른 방법으로 호출되도록) `B` `NEWB`의 이름을 `C` 로 바꾸고 있는 대로 유지합니다. 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

다음 명령 시퀀스:
1. 새 임시 테이블 을 만듭니다.
1. 기존 또는 기존 테이블 또는 기존 테이블새 테이블으로 바꿉습니다.

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```