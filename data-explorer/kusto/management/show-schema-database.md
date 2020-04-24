---
title: .show 데이터베이스 스키마 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .show 데이터베이스 스키마에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519808"
---
# <a name="show-databases-schema"></a>데이터베이스 스키마 표시

단일 테이블 또는 JSON 개체에 모든 테이블과 열이 있는 선택한 데이터베이스 구조의 플랫 목록을 반환합니다.
버전과 함께 사용하면 데이터베이스가 제공된 버전보다 최신 버전인 경우에만 데이터베이스가 반환됩니다.

> [!NOTE]
> 버전은 "vMM.mm" 형식으로만 제공되어야 합니다. MM은 주 버전을 나타내고 mm는 부 버전을 나타냅니다.

`.show``database` *데이터베이스* `schema` 이름`details`[`if_later_than` [ [ [ *"버전"*] 

`.show``database` *데이터베이스* `schema` 이름`if_later_than` [ *"버전"*] `as``json`
 
`.show``databases` `,` 데이터베이스이름1 ... *DatabaseName1* `(` `)` `schema` [`details` | `as` `json`]
 
`.show``databases` `,` *"Version"* *DatabaseName1* 데이터베이스Name1 if_later_than "버전"... `(` `)` `schema` [`details` | `as` `json`]

**예제** 
 
데이터베이스 'TestDB'에는 '이벤트'라는 테이블이 1개 있습니다.

```
.show database TestDB schema 
```

**예제 출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaulttable|IsDefault열|예쁜 이름|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 이름|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| City|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True|| 

**예제** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**예제 출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaulttable|IsDefault열|예쁜 이름|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 이름|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| City|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True||  

현재 데이터베이스 버전보다 낮은 버전이 제공되었기 때문에 'TestDB' 스키마가 반환되었습니다. 동일하거나 더 높은 버전을 제공하면 빈 결과가 생성됩니다.

**예제** 
 
```
.show database TestDB schema as json
```

**예제 출력**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

