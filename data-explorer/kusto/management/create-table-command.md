---
title: . create table-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 만들기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8cfdbe1420745620fcaaf6af81e4f750ca25c1cd
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321610"
---
# <a name="create-table"></a>.create table

비어 있는 새 테이블을 만듭니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.create``table` *TableName* ([columnName: columnType], ...)  [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ]

테이블이 이미 있는 경우이 명령은 success를 반환 합니다.

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
> 여러 테이블을 만들려면 명령을 사용 하 여 [`.create tables`](create-tables-command.md) 클러스터의 성능을 향상 하 고 부하를 줄입니다.
