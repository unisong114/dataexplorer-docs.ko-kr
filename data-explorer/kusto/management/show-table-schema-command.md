---
title: . 테이블 스키마 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 스키마 표시를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: e2a550f0ea755181d39524876833cff4281608b4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618346"
---
# <a name="show-table-schema"></a>.show table schema

Create/alter 명령 및 추가 테이블 메타 데이터에 사용할 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show table TableName cslschema 
```

| 출력 매개 변수 | Type   | 설명                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 테이블의 이름입니다.                                    |
| 스키마           | String | 테이블 만들기/변경에 테이블 스키마를 사용 해야 합니다. |
| DatabaseName     | String | 테이블이 속한 데이터베이스입니다.                   |
| 폴더           | String | 테이블의 폴더                                            |
| DocString        | String | 테이블의 docstring                                         |


## <a name="show-table-schema-as-json"></a>. 테이블 스키마를 JSON으로 표시

JSON 형식 및 추가 테이블 메타 데이터의 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show table TableName schema as JSON
```

| 출력 매개 변수 | Type   | 설명                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 테이블의 이름입니다.                   |
| 스키마           | String | JSON 형식의 테이블 스키마         |
| DatabaseName     | String | 테이블이 속한 데이터베이스입니다. |
| 폴더           | String | 테이블의 폴더                          |
| DocString        | String | 테이블의 docstring                       |
