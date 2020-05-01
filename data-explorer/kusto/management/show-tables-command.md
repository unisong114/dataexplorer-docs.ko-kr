---
title: . 테이블 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 표시를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618329"
---
# <a name="show-tables"></a>. 테이블 표시

데이터베이스의 지정 된 테이블 또는 모든 테이블을 포함 하는 집합을 반환 합니다.

[데이터베이스 뷰어 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**출력**

|출력 매개 변수 |Type |설명
|---|---|---
|TableName  |String |테이블의 이름입니다.
|DatabaseName  |String |테이블이 속한 데이터베이스입니다.
|폴더 |String |테이블의 폴더입니다.
|DocString |String |테이블을 문서화 하는 문자열입니다.

**출력 예제**

|테이블 이름 |데이터베이스 이름 |폴더 | DocString
|---|---|---|---
|Table1 |DB1 |로그 |서비스 로그 포함
|테이블2 |DB1 | 보고 |
|Table3 |DB1 | | 확장 정보 |
|Table4 |DB2 | 메트릭| 서비스 성능 정보 포함
