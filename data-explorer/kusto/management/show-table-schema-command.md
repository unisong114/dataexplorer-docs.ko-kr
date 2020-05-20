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
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555018"
---
# <a name="show-table-schema"></a>.show table schema

Create/alter 명령 및 추가 테이블 메타 데이터에 사용할 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show table TableName cslschema 
```

| 출력 매개 변수 | 유형   | 설명                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | 문자열 | 테이블의 이름입니다.                                    |
| 스키마           | 문자열 | 테이블 만들기/변경에 테이블 스키마를 사용 해야 합니다. |
| DatabaseName     | 문자열 | 테이블이 속한 데이터베이스입니다.                   |
| 폴더           | 문자열 | 테이블의 폴더                                            |
| DocString        | 문자열 | 테이블의 docstring                                         |


## <a name="show-table-schema-as-json"></a>. 테이블 스키마를 JSON으로 표시

JSON 형식 및 추가 테이블 메타 데이터의 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

```kusto
.show table TableName schema as json
```

| 출력 매개 변수 | 유형   | 설명                             |
|------------------|--------|-----------------------------------------|
| TableName        | 문자열 | 테이블의 이름입니다.                   |
| 스키마           | 문자열 | JSON 형식의 테이블 스키마         |
| DatabaseName     | 문자열 | 테이블이 속한 데이터베이스입니다. |
| 폴더           | 문자열 | 테이블의 폴더                          |
| DocString        | 문자열 | 테이블의 docstring                       |
