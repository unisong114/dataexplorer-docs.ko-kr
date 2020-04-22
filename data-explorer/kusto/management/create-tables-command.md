---
title: .create 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .create 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744035"
---
# <a name="create-tables"></a>.create tables

새 빈 테이블을 대량 작업으로 만듭니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행되어야 합니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.create``tables` *테이블Name1([열* 이름:열유형], ...) [`,` *표Name2* ([열 이름 :열 유형], ...) ... ]

테이블이 이미 있는 경우 명령이 성공을 반환합니다.
 
**예제** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
