---
title: . 데이터베이스 스키마 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 데이터베이스 스키마를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616986"
---
# <a name="show-databases-schema"></a>. 데이터베이스 스키마 표시

단일 테이블 또는 JSON 개체의 모든 테이블과 열을 사용 하 여 선택한 데이터베이스 구조의 단순 목록을 반환 합니다.
버전에서 사용 하는 경우 데이터베이스가 제공 된 버전 보다 최신 버전인 경우에만 반환 됩니다.

> [!NOTE]
> 버전은 "vMM.mm" 형식 으로만 제공 되어야 합니다. MM은 주 버전을 나타내고 mm은 부 버전을 나타냅니다.

`.show``database` *DatabaseName* DatabaseName `schema` [`details`] [`if_later_than` *"Version"*] 

`.show``database` *DatabaseName* DatabaseName `schema` [`if_later_than` *"Version"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* ... `(` `)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` *DatabaseName1* DatabaseName1 if_later_than "Version" ... `(` `)` `schema` [`details` | `as` `json`]

**예제** 
 
' TestDB ' 데이터베이스에는 ' Events ' 라는 테이블이 하나 있습니다.

```kusto
.show database TestDB schema 
```

**예제 출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 속성|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| City|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True|| 

**예제** 
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**예제 출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 속성|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| City|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True||  

현재 데이터베이스 버전 보다 낮은 버전이 제공 되었으므로 ' TestDB ' 스키마가 반환 되었습니다. 같거나 더 높은 버전을 제공 하면 빈 결과가 생성 됩니다.

**예제** 
 
```kusto
.show database TestDB schema as json
```

**예제 출력**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

