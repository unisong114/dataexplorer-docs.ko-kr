---
title: . 테이블 만들기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 만들기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321593"
---
# <a name="create-tables"></a>.create tables

대량 작업으로 빈 테이블을 새로 만듭니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.create``tables` *TableName1* ([columnname: columnType], ...) [ `,` *TableName2* ([columnname: columnType], ...) ...] [ `with` `(` `folder` `=` *FolderName*] `)` ]

테이블이 이미 있는 경우이 명령은 success를 반환 합니다.
 
**예제** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
