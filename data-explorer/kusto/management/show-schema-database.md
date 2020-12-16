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
ms.openlocfilehash: 4f3639aeb6e401aa37703bbef929af2275960a91
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513982"
---
# <a name="show-database-schema-commands"></a>. 데이터베이스 스키마 명령 표시

다음 명령은 데이터베이스 스키마를 테이블, JSON 개체 또는 CSL 스크립트로 표시 합니다.

## <a name="show-databases-schema"></a>. 데이터베이스 스키마 표시

**구문**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *"Version"*] 

`.show``databases` `(`  `,` DatabaseName1 ... `)` `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema``details`

**반환**

단일 테이블 또는 JSON 개체의 모든 테이블과 열을 사용 하 여 선택한 데이터베이스 구조의 단순 목록을 반환 합니다.
버전에서 사용 하는 경우 데이터베이스가 제공 된 버전 보다 최신 버전인 경우에만 반환 됩니다.

> [!NOTE]
> 버전은 "vMM.mm" 형식 으로만 제공 되어야 합니다. MM은 주 버전을 나타내고 mm은 부 버전을 나타냅니다.

**예제** 
 
' TestDB ' 데이터베이스에는 ' Events ' 라는 테이블이 하나 있습니다.

```kusto
.show database TestDB schema 
```

**출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 이름|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| 구/군/시|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True|| 

**예제** 

다음 예에서는 데이터베이스가 제공 된 버전 보다 최신 버전인 경우에만 반환 됩니다.
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**출력**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|버전
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|이벤트|||True|False||       
|TestDB|이벤트| 이름|System.String|True|False||     
|TestDB|이벤트| StartTime|  System.DateTime|True|False||    
|TestDB|이벤트| EndTime|    System.DateTime|True|False||        
|TestDB|이벤트| 구/군/시|   System.String|True| False||     
|TestDB|이벤트| SessionId|  System.Int32|True|  True||  

현재 데이터베이스 버전 보다 낮은 버전이 제공 되었으므로 ' TestDB ' 스키마가 반환 되었습니다. 같거나 더 높은 버전을 제공 하면 빈 결과가 생성 됩니다.

## <a name="show-database-schema-as-json"></a>. 데이터베이스 스키마를 json으로 표시

**구문**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"Version"*] `as``json`
 
`.show``databases` `(`  `,` DatabaseName1 ... `)` `schema` `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` `)` `schema` ... `as``json`

**반환**

모든 테이블 및 열을 JSON 개체로 사용 하 여 선택한 데이터베이스 구조에 대 한 단순 목록을 반환 합니다.
버전에서 사용 하는 경우 데이터베이스가 제공 된 버전 보다 최신 버전인 경우에만 반환 됩니다.

**예제** 
 
```kusto
.show database TestDB schema as json
```

**출력**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>. 데이터베이스 스키마를 csl 스크립트로 표시

지정 된 (또는 현재) 데이터베이스 스키마의 복사본을 만드는 데 필요한 모든 명령이 포함 된 CSL 스크립트를 생성 합니다.

**구문**

`.show``database`  `schema` DatabaseName `as` `csl` `script` [ `with(` *Options* `)` ]

**인수**

다음 *옵션* 은 모두 선택 사항입니다.

* `IncludeEncodingPolicies`: ( `true`  |  `false` )-인 경우 `true` 데이터베이스/테이블/열 수준의 인코딩 정책이 포함 됩니다. 기본값은 `false`입니다. 
* `IncludeSecuritySettings`: ( `true`  |  `false` )-기본값은 `false` 입니다. 인 경우 `true` 다음 옵션이 포함 됩니다.
  * 데이터베이스/테이블 수준의 인증 된 보안 주체입니다.
  * 테이블 수준의 행 수준 보안 정책입니다.
  * 테이블 수준에서 제한 된 보기 액세스 정책입니다.
* `IncludeIngestionMappings`: ( `true`  |  `false` )-인 경우 `true` 테이블 수준에서 수집 매핑이 포함 됩니다. 기본값은 `false`입니다. 

**반환**

문자열로 반환 된 스크립트에는 다음이 포함 됩니다.

* 데이터베이스의 모든 테이블을 만드는 명령입니다.
* 모든 데이터베이스/테이블/열 정책을 원래 정책과 일치 하도록 설정 하는 명령입니다.
* 데이터베이스의 모든 사용자 정의 함수를 만들거나 변경 하는 명령입니다.

**예제** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
