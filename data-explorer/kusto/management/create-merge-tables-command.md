---
title: .create-병합 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .create 병합 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744437"
---
# <a name="create-merge-tables"></a>.create-병합 테이블

특정 데이터베이스의 컨텍스트에서 단일 대량 작업에서 기존 테이블의 스키마를 만들거나 확장할 수 있습니다.

기존 테이블을 확장하기 위한 [테이블 관리자 권한뿐만](../management/access-control/role-based-authorization.md) 아니라 데이터베이스 [사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.create-merge``tables` *테이블Name1([열* 이름:열유형], ...) [`,` *표Name2* ([열 이름 :열 유형], ...) ... ]

* 존재하지 않는 지정된 테이블이 만들어집니다.
* 이미 있는 지정된 테이블에는 스키마가 확장됩니다.
    * 기존 테이블의 스키마 _끝에_ 존재하지 않는 열이 추가됩니다.
    * 명령에 지정되지 않은 기존 열은 기존 테이블의 스키마에서 제거되지 않습니다.
    * 명령에서 다른 데이터 유형으로 지정된 기존 열은 기존 테이블의 스키마에서 실패로 이어질 것입니다(테이블이 만들어지거나 확장되지 않음).

**예제** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**출력 반환**

| TableName | DatabaseName  | 폴더 | 닥스트링 (것)과 함께 |
|-----------|---------------|--------|-----------|
| 마이로그    | 맨 위 비교 |        |           |
| 마이유저   | 맨 위 비교 |        |           |
