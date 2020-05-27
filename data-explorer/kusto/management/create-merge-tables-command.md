---
title: . create-merge tables-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의. create-merge 테이블 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011519"
---
# <a name="create-merge-tables"></a>. create-merge 테이블

를 사용 하면 특정 데이터베이스의 컨텍스트에서 하나의 대량 작업으로 기존 테이블의 스키마를 만들고 확장할 수 있습니다.

> [!NOTE]
> [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
> 기존 테이블을 확장 하려면 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

**구문**

`.create-merge``tables` *TableName1* ([columnname: columnType], ...) [ `,` *TableName2* ([columnname: columnType], ...) ...]

* 존재 하지 않는 지정 된 테이블이 생성 됩니다.
* 이미 존재 하는 지정 된 테이블에는 해당 스키마가 확장 됩니다.
    * 존재 하지 않는 열은 기존 테이블의 스키마 _끝_ 에 추가 됩니다.
    * 명령에 지정 되지 않은 기존 열은 기존 테이블의 스키마에서 제거 되지 않습니다.
    * 기존 테이블의 스키마에 있는 것과 다른 명령에서 데이터 형식으로 지정 된 기존 열은 오류를 발생 합니다. 테이블이 만들어지거나 확장 되지 않습니다.

**예제**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**반환 출력**

| TableName | DatabaseName  | 폴더 | DocString |
|-----------|---------------|--------|-----------|
| Mylogs)    | TopComparison |        |           |
| MyUsers   | TopComparison |        |           |
