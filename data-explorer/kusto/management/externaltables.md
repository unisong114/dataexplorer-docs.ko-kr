---
title: 외부 테이블 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744720"
---
# <a name="external-table-management"></a>외부 테이블 관리

[외부 테이블에](../query/schema-entities/externaltables.md) 대한 개요는 외부 테이블을 참조하십시오. 

## <a name="common-external-tables-control-commands"></a>일반적인 외부 테이블 제어 명령

다음 명령은 _모든_ 유형의 외부 테이블과 관련이 있습니다.

### <a name="show-external-tables"></a>외부 테이블 을 표시합니다.

* 데이터베이스(또는 특정 외부 테이블)의 모든 외부 테이블을 반환합니다.
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문:** 

`.show` `external` `tables`

`.show``external` *이름* `table`

**출력**

| 출력 매개 변수 | Type   | Description                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 문자열 | 외부 테이블 이름                                             |
| TableType        | 문자열 | 외부 테이블의 유형                                              |
| 폴더           | 문자열 | 테이블 폴더                                                     |
| 닥스트링 (것)과 함께        | 문자열 | 테이블을 문서화하는 문자열                                       |
| 속성       | 문자열 | 테이블의 JSON 직렬화된 속성(테이블 유형에 따라 다릅니다). |


**예:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 폴더         | 닥스트링 (것)과 함께 | 속성 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | 외부 테이블 | Docs      | {}         |


### <a name="show-external-table-schema"></a>외부 테이블 스키마 표시

* 외부 테이블의 스키마를 JSON 또는 CSL로 반환합니다. 
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문:** 

`.show``external` `as` `json` |  *TableName* `schema` 테이블 이름`csl`() `table`

`.show``external` *이름* `table``cslschema`

**출력**

| 출력 매개 변수 | Type   | Description                        |
|------------------|--------|------------------------------------|
| TableName        | 문자열 | 외부 테이블 이름            |
| 스키마           | 문자열 | JSON 형식의 테이블 스키마 |
| DatabaseName     | 문자열 | 테이블의 데이터베이스 이름             |
| 폴더           | 문자열 | 테이블 폴더                    |
| 닥스트링 (것)과 함께        | 문자열 | 테이블을 문서화하는 문자열      |

**예:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**출력:**

*Json:*

| TableName | 스키마    | DatabaseName | 폴더         | 닥스트링 (것)과 함께 |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"이름":"외부블blob",<br>"폴더":"외부 테이블",<br>"닥스트링":"문서",<br>"정렬 된 열":[{"이름":"x","유형":"System.Int64","CslType":"긴","DocString":"},"},"이름":"의","유형":"시스템.문자열","CslType":"문자열","DocString":"}}}} | DB           | 외부 테이블 | Docs      |


*Csl:*

| TableName | 스키마          | DatabaseName | 폴더         | 닥스트링 (것)과 함께 |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x : 긴, s : 문자열 | DB           | 외부 테이블 | Docs      |

### <a name="drop-external-table"></a>외부 테이블 을 놓습니다.

* 외부 테이블 삭제 
* 이 작업 이후에는 외부 테이블 정의를 복원할 수 없습니다.
* [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문:**  

`.drop``external` *이름* `table`

**출력**

삭제된 테이블의 속성을 반환합니다. [.show 외부 테이블을 참조하십시오.](#show-external-tables)

**예:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 폴더         | 닥스트링 (것)과 함께 | 스키마       | 속성 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | 외부 테이블 | Docs      | [{ "이름": "x", "CslType": "long"},<br> { "이름": "s", "CslType": "문자열" }] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure 저장소 또는 Azure 데이터 레이크의 외부 테이블

다음 명령은 외부 테이블을 만드는 방법을 설명합니다. 테이블은 Azure Blob 저장소, Azure 데이터 레이크 저장소 Gen1 또는 Azure 데이터 레이크 저장소 Gen2에 있을 수 있습니다. 
[저장소 연결 문자열은](../api/connection-strings/storage.md) 이러한 각 옵션에 대한 연결 문자열을 만드는 것을 설명합니다. 

### <a name="create-or-alter-external-table"></a>외부 테이블을 만들거나 .alter

**구문**

`.create` | `.alter`() `external` *TableName* 표 이름 *(스키마)* `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *파티션* [`,` ....]]  
`dataformat``=` *형식 지정*  
`(`  
*스토리지연결문자열* `,` [...]  
`)`  
[`with` `(``docstring` `=` [ [`,` `folder` `=` *문서*] [ *폴더 이름*] , *property_name* `=` *값*`,`... `)`]

명령이 실행되는 데이터베이스에서 새 외부 테이블을 만들거나 변경합니다.

**매개 변수**

* *테이블 이름* - 외부 테이블 이름입니다. [엔터티 이름에](../query/schema-entities/entity-names.md)대한 규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *스키마* - 형식의 외부 데이터 `ColumnName:ColumnType[, ColumnName:ColumnType ...]`스키마: . 외부 데이터 스키마를 알 수 없는 경우 [infer_storage_schema](../query/inferstorageschemaplugin.md) 플러그인을 사용하여 외부 파일 내용을 기반으로 스키마를 추론할 수 있습니다.
* *파티션* - 하나 또는 여러 파티션 정의(선택 사항). 아래 파티션 구문을 참조하십시오.
* *형식* - 데이터 형식입니다. 모든 [인기 형식은](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) 쿼리에 지원됩니다. [내보내기 시나리오에](data-export/export-data-to-an-external-table.md) 외부 테이블을 사용하는 것은 `CSV`다음과 `TSV` `JSON`같은 `Parquet`형식으로 제한됩니다.
* *StorageConnectionString* - 자격 증명을 포함하여 Azure Blob Storage Blob 컨테이너 또는 Azure Data Lake 스토어 파일 시스템(가상 디렉터리 또는 폴더)에 대한 하나 또는 여러 경로입니다. 자세한 내용은 [저장소 연결 문자열을](../api/connection-strings/storage.md) 참조하십시오. 많은 양의 데이터를 외부 테이블로 [내보내는](data-export/export-data-to-an-external-table.md) 경우 저장소 제한을 피하기 위해 두 개 이상의 저장소 계정을 제공하는 것이 좋습니다. 내보내기는 제공된 모든 계정 간에 쓰기를 배포합니다. 

**파티션 구문**

[`format_datetime =` *날짜 시간 파티션 형식*] `bin(` *타임스탬프열 이름,* *파티션시간*`)`  
|   
[*문자열 형식 사전 픽스*] *문자열열 이름* [*문자열 형식Suffix*])

**파티션 매개 변수**

* *DateTimePartitionFormat* - 출력 경로에 필요한 디렉터리 구조의 형식(선택 사항)입니다. 분할이 정의되고 형식이 지정되지 않은 경우 기본값은 PartitionByTimeSpan에 따라 "yyyy/MM/dd/HH/mm"입니다. 예를 들어 1d로 분할하는 경우 구조는 "yyyy/MM/dd"가 됩니다. 당신이 1h로 분할하는 경우, 구조는 "yyyy/MM/DD/HH"가 됩니다.
* *타임스탬프열이름* - 테이블이 분할된 일시 시간 열입니다. 외부 테이블로 내보낼 때 타임스탬프 열은 외부 테이블 스키마 정의 및 내보내기 쿼리의 출력에 있어야 합니다.
* *파티션ByTimeSpan* - 파티션할 시간 범위 리터럴입니다.
* *StringFormatPrefix* - 테이블 값(선택 사항) 앞에 연결되는 아티팩트 경로의 일부가 될 상수 문자열 리터럴입니다.
* *StringFormatSuffix* - 테이블 값(선택 사항) 다음으로 연결되는 아티팩트 경로의 일부가 될 상수 문자열 리터럴입니다.
* *StringColumnName* - 테이블이 분할된 문자열 열입니다. 문자열 열은 외부 테이블 스키마 정의에 있어야 합니다.

**선택적 속성**:

| 속성         | Type     | Description       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 테이블 폴더                                                                     |
| `docString`      | `string` | 테이블을 문서화하는 문자열                                                       |
| `compressed`     | `bool`   | 설정된 경우 Blob이 파일로 `.gz` 압축되는지 여부를 나타냅니다.                  |
| `includeHeaders` | `string` | CSV 또는 TSV Blob의 경우 Blob에 헤더가 포함되어 있는지 여부를 나타냅니다.                     |
| `namePrefix`     | `string` | 설정된 경우 Blob의 접두사를 나타냅니다. 쓰기 작업에서 모든 Blob이 이 접두사로 작성됩니다. 읽기 작업에서는 이 접두사가 있는 Blob만 읽습니다. |
| `fileExtension`  | `string` | 설정된 경우 Blob의 파일 확장을 나타냅니다. 쓰기시 Blob 이름은 이 접미사로 끝납니다. 읽을 때 이 파일 확장자만 읽을 수 있습니다.           |
| `encoding`       | `string` | 텍스트가 인코딩되는 방법을 `UTF8NoBOM` 나타냅니다( 기본값) 또는 `UTF8BOM`.             |

> [!NOTE]
> * 테이블이 있으면 `.create` 오류가 발생하면 명령이 실패합니다. 기존 `.alter` 테이블을 수정하는 데 사용합니다. 
> * 외부 Blob 테이블의 스키마, 형식 또는 파티션 정의를 변경하는 것은 지원되지 않습니다. 

[에](../management/access-control/role-based-authorization.md) 대한 `.create` 데이터베이스 사용자 권한 및 테이블 관리자 [권한이](../management/access-control/role-based-authorization.md) `.alter`필요합니다. 
 
**예제** 

분할되지 않은 외부 테이블입니다. 모든 아티팩트는 정의된 컨테이너 바로 아래에 있어야 합니다.

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

dateTime별로 분할된 외부 테이블입니다. 아티팩트는 정의된 경로 아래의 "yyyy/MM/dd" 형식의 디렉토리에 있습니다.

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

dateTime별로 분할된 외부 테이블은 "year=yyyy/month=MM/day=dd"의 디렉토리 형식으로 분할됩니다.

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

월별 데이터 파티션과 "yyyy/MM"의 디렉터리 형식이 있는 외부 테이블:

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

파티션이 두 개 있는 외부 테이블입니다. 디렉터리 구조는 두 파티션의 연결입니다. 예를 들어 "고객 이름=소프트웍스/2011/11/11":

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**출력**

|TableName|TableType|폴더|닥스트링 (것)과 함께|속성|ConnectionStrings|파티션|
|---|---|---|---|---|---|---|
|외부 다중 파티션|Blob|외부 테이블|Docs|{"Format":"Csv","압축":false,"압축 유형":null,"FileExtension":"csv","포함 헤더":"없음","인코딩":null,"NamePrefix":null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat":"고객 이름={0}","열 이름":"고객 이름","일반":0},PartitionBy":"1.00:00:00","열 이름":"타임스탬프","서수":1}]|

#### <a name="spark-virtual-columns-support"></a>스파크 가상 컬럼 지원

Spark에서 데이터를 내보내면 데이터 프레임 `partitionBy` 작성기의 메서드에 지정된 파티션 열이 데이터 파일에 기록되지 않습니다. 이는 데이터가 이미 "폴더" 이름(예: "폴더" `column1=<value>/column2=<value>/`이름)에 있고 Spark가 읽을 때 이를 인식할 수 있기 때문에 데이터 중복을 방지하기 위해 수행됩니다. 그러나 Kusto는 파티션 열이 데이터 자체에 있어야 합니다. Kusto의 가상 열에 대한 지원이 계획되어 있습니다. 그때까지 Spark에서 데이터를 내보낼 때 데이터 프레임을 작성하기 전에 데이터가 분할된 모든 열의 복사본을 만듭니다.

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto에서 외부 테이블을 정의할 때 다음 예제와 같이 파티션 열을 지정합니다.

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

### <a name="show-external-table-artifacts"></a>외부 테이블 아티팩트 를 표시합니다.

* 지정된 외부 테이블을 쿼리할 때 처리될 모든 아티팩트 목록을 반환합니다.
* [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문:** 

`.show``external` *이름* `table``artifacts`

**출력**

| 출력 매개 변수 | Type   | Description                       |
|------------------|--------|-----------------------------------|
| Uri              | 문자열 | 외부 저장소 아티팩트의 URI |

**예:**

```kusto
.show external table T artifacts
```

**출력:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>외부 테이블 매핑 만들기

`.create``external` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat* 외부 테이블 이름 매핑 이름 매핑InJsonFormat `table`

새 매핑을 만듭니다. 자세한 내용은 [데이터 매핑 을 참조하십시오.](./mappings.md#json-mapping)

**예제** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 이름     | 종류 | 매핑                                                           |
|----------|------|-------------------------------------------------------------------|
| 매핑1 | JSON | [{"열 이름":"행 번호","열 유형":"int","속성","속성":"path":"$.행 번호"}},"{"열 이름":"행이"},"열 유형":"",","속성":"","속성":""path":"$.rowguid}}} |

### <a name="alter-external-table-mapping"></a>외부 테이블 매핑 .alter

`.alter``external` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat* 외부 테이블 이름 매핑 이름 매핑InJsonFormat `table`

기존 매핑을 변경합니다. 
 
**예제** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 이름     | 종류 | 매핑                                                                |
|----------|------|------------------------------------------------------------------------|
| 매핑1 | JSON | [{"열 이름":"행 번호","열 유형":","속성","속성"},"{"Path":"$.행 번호"}},"열 이름":"행이","열 유형":",","속성":"","속성":"","Path":"$.rowguid}}} |

### <a name="show-external-table-mappings"></a>외부 테이블 매핑 .show

`.show``external` *ExternalTableName* `json` `mapping` 외부 테이블 이름 *매핑 이름* `table` 

`.show`외부 *테이블 이름* `json` `external` `table``mappings`

매핑(이름으로 지정된 모든 또는 하나)을 표시합니다.
 
**예제** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**예제 출력**

| 이름     | 종류 | 매핑                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| 매핑1 | JSON | [{"열 이름":"행 번호","열 유형":","속성","속성"},"{"Path":"$.행 번호"}},"열 이름":"행이","열 유형":",","속성":"","속성":"","Path":"$.rowguid}}} |

### <a name="drop-external-table-mapping"></a>.drop 외부 테이블 매핑

`.drop``external` *ExternalTableName* `json` `mapping` 외부 테이블 이름 *매핑 이름* `table` 

데이터베이스에서 매핑을 삭제합니다.
 
**예제** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>외부 SQL 테이블

### <a name="create-or-alter-external-sql-table"></a>.create 또는 외부 SQL 테이블 변경

명령이 실행되는 데이터베이스에서 외부 SQL 테이블을 만들거나 변경합니다.  

**구문**

`.create` | `.alter`)) `external` 테이블 이름([열 이름:열유형]), ...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *SqlTable Name*  
`(`*SqlServer연결스트링*`)`  
[`with` `(``docstring` `=` [ [`,` `folder` `=` *문서*] [ *폴더 이름*] , *property_name* `=` *값*`,`... `)`]


**매개 변수:**

* *테이블 이름* - 외부 테이블 이름입니다. [엔터티 이름에](../query/schema-entities/entity-names.md)대한 규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *SqlTableName* SQL 테이블의 이름입니다.
* *SqlServerConnectionString* SQL 서버에 대한 연결 문자열입니다. 다음 중 하나일 수 있습니다. 
    * **AAD 통합** `Authentication="Active Directory Integrated"`인증 (): 사용자 또는 응용 프로그램은 AAD를 통해 Kusto로 인증하고 동일한 토큰을 사용하여 SQL Server 네트워크 끝점에 액세스합니다.
    * **사용자 이름/암호** `User ID=...; Password=...;`인증 () 외부 테이블이 연속 [내보내기에](data-export/continuous-data-export.md)사용되는 경우 이 메서드를 사용하여 인증을 수행해야 합니다. 

> [!WARNING]
> 기밀 정보가 포함된 연결 문자열및 쿼리는 Kusto 추적에서 생략되도록 난독 처리되어야 합니다. 자세한 내용은 [난독 처리된 문자열 리터럴을](../query/scalar-data-types/string.md#obfuscated-string-literals) 참조하십시오.

**선택적 속성**
| 속성            | Type            | Description                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 테이블의 폴더입니다.                  |
| `docString`         | `string`        | 테이블을 문서화하는 문자열입니다.      |
| `firetriggers`      | `true`/`false`  | 대상 `true`시스템에 SQL 테이블에 정의된 INSERT 트리거를 발생하도록 지시하는 경우 기본값은 `false`입니다. (자세한 내용은 [대량 삽입](https://msdn.microsoft.com/library/ms188365.aspx) 및 [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)참조) |
| `createifnotexists` | `true`/ `false` | 대상 `true`SQL 테이블이 아직 존재하지 않는 경우 . 이 `primarykey` 경우 기본 키인 결과 열을 나타내려면 속성을 제공해야 합니다. 기본값은 `false`입니다.  |
| `primarykey`        | `string`        | `true`이 `createifnotexists` 명령에서 만든 경우 SQL 테이블의 기본 키로 사용되는 결과의 열 이름을 나타냅니다.                  |

> [!NOTE]
> * 테이블이 있으면 오류가 `.create` 발생하면 명령이 실패합니다. 기존 `.alter` 테이블을 수정하는 데 사용합니다. 
> * 외부 SQL 테이블의 스키마 또는 형식을 변경하는 것은 지원되지 않습니다. 

에 대한 `.create` [데이터베이스 사용자 권한](../management/access-control/role-based-authorization.md) 및 테이블 관리자 권한이 필요합니다. [table admin permission](../management/access-control/role-based-authorization.md) `.alter` 
 
**예제** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**출력**

| TableName   | TableType | 폴더         | 닥스트링 (것)과 함께 | 속성                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| 외부 Sql | Sql       | 외부 테이블 | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "대상 엔티티 네임": "MySqlTable",<br>  "대상 엔티티커넥스트링": "서버=tcp:myserver.database.windows.net,1433; 인증=활성 디렉터리 통합;초기 카탈로그=mydatabase;",<br>  "파이어 트리거": 사실,<br>  "만들기IfNotExists": true,<br>  "기본 키": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>SQL 형식의 외부 테이블 쿼리 
다른 유형의 외부 테이블과 유사하게 외부 SQL 테이블을 쿼리하는 것이 지원됩니다. [외부 테이블 쿼리 를](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)참조하십시오. SQL 외부 테이블 쿼리 구현은 SQL 테이블에서 전체 'SELECT *' (또는 관련 열 선택)를 실행하고 나머지 쿼리는 Kusto 측에서 실행됩니다. 다음 외부 테이블 쿼리를 고려하십시오. 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto는 SQL 데이터베이스에 'SELECT * SELECT *' 쿼리를 실행한 다음 Kusto 측에서 카운트를 실행합니다. 이러한 경우 외부 테이블 함수를 사용하는 대신 T-SQL('SELECT COUNT(1) FROM TABLE')을 직접 작성하고 [sql_request 플러그인을](../query/sqlrequestplugin.md)사용하여 실행하면 성능이 향상될 것으로 예상됩니다. 마찬가지로 필터는 SQL 쿼리에 푸시되지 않습니다.  
외부 테이블을 사용하여 Kusto 측에서 추가 실행을 위해 쿼리에서 전체 테이블(또는 관련 열)을 읽어야 하는 경우 SQL 테이블을 쿼리하는 것이 좋습니다. T-SQL에서 SQL 쿼리를 크게 최적화할 수 있는 경우 [sql_request 플러그인을](../query/sqlrequestplugin.md)사용합니다.
