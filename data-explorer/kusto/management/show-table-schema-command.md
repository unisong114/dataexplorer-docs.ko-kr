---
title: .show 테이블 스키마 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .show 테이블 스키마에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519774"
---
# <a name="show-table-schema"></a>.show 테이블 스키마

명령 만들기/변경 명령 및 추가 테이블 메타데이터에 사용할 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

```
.show table TableName cslschema 
```
| 출력 매개 변수 | Type   | Description                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 테이블의 이름입니다.                                    |
| 스키마           | String | 테이블 만들기/변경에 사용해야 하는 테이블 스키마 |
| DatabaseName     | String | 테이블이 속한 데이터베이스                   |
| 폴더           | String | 테이블 폴더                                            |
| 닥스트링 (것)과 함께        | String | 테이블의 문서 문자열                                         |


## <a name="show-table-schema-as-json"></a>JSON으로 테이블 스키마 표시

JSON 형식및 추가 테이블 메타데이터로 스키마를 가져옵니다.

[데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

```
.show table TableName schema as JSON
```

| 출력 매개 변수 | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 테이블의 이름입니다.                   |
| 스키마           | String | JSON 형식의 테이블 스키마         |
| DatabaseName     | String | 테이블이 속한 데이터베이스 |
| 폴더           | String | 테이블 폴더                          |
| 닥스트링 (것)과 함께        | String | 테이블의 문서 문자열                       |