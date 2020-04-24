---
title: .show 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .show 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519638"
---
# <a name="show-tables"></a>테이블 .show

지정된 테이블 또는 데이터베이스의 모든 테이블을 포함하는 집합을 반환합니다.

[데이터베이스 뷰어 권한이](../management/access-control/role-based-authorization.md)필요합니다.

```
.show tables
.show tables (T1, ..., Tn)
```

**출력**

|출력 매개 변수 |Type |Description
|---|---|---
|TableName  |String |테이블의 이름입니다.
|DatabaseName  |String |테이블이 속한 데이터베이스입니다.
|폴더 |String |테이블의 폴더입니다.
|닥스트링 (것)과 함께 |String |테이블을 문서화하는 문자열입니다.

**출력 예제**

|테이블 이름 |데이터베이스 이름 |폴더 | 닥스트링 (것)과 함께
|---|---|---|---
|Table1 |DB1 |로그 |서비스 로그 포함
|테이블2 |DB1 | 보고 |
|Table3 |DB1 | | 확장 정보 |
|표 4 |DB2 | 메트릭| 서비스 성능 정보 포함