---
title: 외부 테이블 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: c52f0649531678e31310f5a1f4bfb97f99f15857
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741934"
---
# <a name="external-table-management"></a>외부 테이블 관리

외부 테이블에 대 한 개요는 [외부 테이블](../query/schema-entities/externaltables.md) 을 참조 하세요. 

## <a name="common-external-tables-control-commands"></a>일반적인 외부 테이블 제어 명령

다음 _명령은 모든 형식의 외부 테이블과_ 관련이 있습니다.

### <a name="show-external-tables"></a>. 외부 테이블 표시

* 데이터베이스 (또는 특정 외부 테이블)의 모든 외부 테이블을 반환 합니다.
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show` `external` `tables`

`.show``external` *TableName* TableName `table`

**출력**

| 출력 매개 변수 | 형식   | 설명                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | 외부 테이블의 이름                                             |
| TableType        | string | 외부 테이블의 유형입니다.                                              |
| 폴더           | string | 테이블의 폴더                                                     |
| DocString        | string | 테이블을 문서화 하는 문자열                                       |
| 속성       | string | 테이블의 JSON 직렬화 된 속성 (테이블 형식에만 해당) |


**예:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 폴더         | DocString | 속성 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


### <a name="show-external-table-schema"></a>. 외부 테이블 스키마 표시

* 외부 테이블의 스키마를 JSON 또는 CSL로 반환 합니다. 
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show``external` `schema` *TableName* (`json`) `table` `as`  | `csl`

`.show``external` *TableName* TableName `table``cslschema`

**출력**

| 출력 매개 변수 | 형식   | 설명                        |
|------------------|--------|------------------------------------|
| TableName        | string | 외부 테이블의 이름            |
| 스키마           | string | JSON 형식의 테이블 스키마 |
| DatabaseName     | string | 테이블의 데이터베이스 이름             |
| 폴더           | string | 테이블의 폴더                    |
| DocString        | string | 테이블을 문서화 하는 문자열      |

**예:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**출력**

*n*

| TableName | 스키마    | DatabaseName | 폴더         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "ExternalBlob",<br>"폴더": "ExternalTables",<br>"DocString": "Docs",<br>"OrderedColumns": [{"Name": "x", "Type": "system.string", "CslType": "long", "DocString": ""}, {"Name": "s", "Type": "system.string", "CslType": "String", "DocString": ""}]} | DB           | ExternalTables | Docs      |


*csl:*

| TableName | 스키마          | DatabaseName | 폴더         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long, 토 문자열 | DB           | ExternalTables | Docs      |

### <a name="drop-external-table"></a>. drop external table

* 외부 테이블을 삭제 합니다. 
* 이 작업을 수행 하면 외부 테이블 정의를 복원할 수 없습니다.
* [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:**  

`.drop``external` *TableName* TableName `table`

**출력**

삭제 된 테이블의 속성을 반환 합니다. 을 참조 하십시오 [. 외부 테이블을 표시](#show-external-tables)합니다.

**예:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 폴더         | DocString | 스키마       | 속성 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{"Name": "x", "CslType": "long"},<br> {"Name": "s", "CslType": "string"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage 또는 Azure Data Lake의 외부 테이블

다음 명령은 외부 테이블을 만드는 방법을 설명 합니다. 테이블은 Azure Blob Storage, Azure Data Lake Store Gen1 또는 Azure Data Lake Store Gen2에서 찾을 수 있습니다. 
[저장소 연결 문자열](../api/connection-strings/storage.md) 에서는 이러한 각 옵션에 대 한 연결 문자열을 만드는 방법을 설명 합니다. 

### <a name="create-or-alter-external-table"></a>. create 또는. alter external table

**구문**

`.create` | (`.alter`) `external` *TableName (* *스키마*) `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* [`,` ...]]  
`dataformat``=` *형식*  
`(`  
*StorageConnectionString* [`,` ...]  
`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* 설명서] [`,` FolderName], property_name 값 ... `=` `)`]

명령이 실행 되는 데이터베이스에서 새 외부 테이블을 만들거나 변경 합니다.

**매개 변수**

* *TableName* -외부 테이블 이름입니다. [엔터티 이름](../query/schema-entities/entity-names.md)규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *스키마* -형식의 외부 데이터 스키마: `ColumnName:ColumnType[, ColumnName:ColumnType ...]`. 외부 데이터 스키마를 알 수 없는 경우에는 외부 파일 내용에 따라 스키마를 유추할 수 있는 [infer_storage_schema](../query/inferstorageschemaplugin.md) 플러그 인을 사용 합니다.
* *파티션* -하나 또는 여러 파티션 정의 (옵션). 아래의 파티션 구문을 참조 하세요.
* *Format* -데이터 형식입니다. 수집 [형식은](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) 쿼리를 위해 지원 됩니다. [내보내기 시나리오](data-export/export-data-to-an-external-table.md) 에 외부 테이블을 사용 하는 `CSV`것은, `TSV`, `JSON`, `Parquet`형식으로 제한 됩니다.
* *StorageConnectionString* -자격 증명을 포함 하 여 Blob 컨테이너 또는 Azure Data Lake Store 파일 시스템 (가상 디렉터리 또는 폴더)을 Azure Blob Storage 하는 하나 또는 여러 경로입니다. 자세한 내용은 [저장소 연결 문자열](../api/connection-strings/storage.md) 을 참조 하세요. 많은 양의 데이터를 외부 테이블로 [내보내는](data-export/export-data-to-an-external-table.md) 경우 저장소 제한을 방지 하기 위해 단일 저장소 계정을 제공 하는 것이 좋습니다. 내보내기는 제공 된 모든 계정 간에 쓰기를 배포 합니다. 

**파티션 구문**

[`format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *파티션*`)`  
|   
[*Stringformatprefix*] *Stringcolumnname* [*stringformatsuffix*])

**파티션 매개 변수**

* *DateTimePartitionFormat* -출력 경로에서 필요한 디렉터리 구조의 형식입니다 (옵션). 분할이 정의 되 고 형식이 지정 되지 않은 경우, 기본값은 분할을 기반으로 하는 "yyyy/MM/dd/HH/mm"입니다. 예를 들어 1d로 분할 하는 경우 structure는 "yyyy/MM/dd"입니다. 1 시간로 분할 하는 경우 구조는 "yyyy/MM/dd/HH"가 됩니다.
* 테이블이 분할 된 *TimestampColumnName* -Datetime 열입니다. 외부 테이블로 내보낼 때 타임 스탬프 열은 외부 테이블 스키마 정의 및 내보내기 쿼리의 출력에 있어야 합니다.
* Partition *bytimespan* -분할할 timespan 리터럴입니다.
* *Stringformatprefix* -아티팩트 경로의 일부가 되 고, 테이블 값 앞에 연결 된 상수 문자열 리터럴입니다 (옵션).
* *Stringformatsuffix* -아티팩트 경로의 일부가 되 고 테이블 값 (선택 사항) 다음에 연결 되는 상수 문자열 리터럴입니다.
* *Stringcolumnname* -테이블이 분할 된 문자열 열입니다. 문자열 열은 외부 테이블 스키마 정의에 있어야 합니다.

**선택적 속성**:

| 속성         | Type     | 설명       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 테이블의 폴더                                                                     |
| `docString`      | `string` | 테이블을 문서화 하는 문자열                                                       |
| `compressed`     | `bool`   | 설정 하는 경우 blob이 `.gz` 파일로 압축 되는지 여부를 나타냅니다.                  |
| `includeHeaders` | `string` | CSV 또는 TSV blob의 경우 blob에 헤더가 포함 되는지 여부를 나타냅니다.                     |
| `namePrefix`     | `string` | 설정 하는 경우 blob의 접두사를 나타냅니다. 쓰기 작업 시 모든 blob이이 접두사로 작성 됩니다. 읽기 작업에서는이 접두사가 있는 blob만 읽습니다. |
| `fileExtension`  | `string` | 설정 하는 경우 blob의 파일 확장명을 나타냅니다. 쓰기에서는 blob 이름이이 접미사로 종료 됩니다. 읽을 때이 파일 확장명을 가진 blob만 읽습니다.           |
| `encoding`       | `string` | 텍스트를 인코딩하는 방법을 나타냅니다. `UTF8NoBOM` (기본값) 또는 `UTF8BOM`입니다.             |

쿼리의 외부 테이블 매개 변수에 대 한 자세한 내용은 [아티팩트 필터링 논리](#artifact-filtering-logic)를 참조 하세요.

> [!NOTE]
> * 테이블이 있으면 오류가 발생 하 `.create` 여 명령이 실패 합니다. 기존 `.alter` 테이블을 수정 하는 데 사용 합니다. 
> * 외부 blob 테이블의 스키마, 형식 또는 파티션 정의 변경은 지원 되지 않습니다. 

`.create` 에 대 한 [데이터베이스 사용자 권한](../management/access-control/role-based-authorization.md) 및에 대 한 `.alter` [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다. 
 
**예제** 

분할 되지 않은 외부 테이블입니다. 모든 아티팩트가 정의 된 컨테이너 바로 아래에 있어야 합니다.

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
   folder = "ExternalTables"
)  
```

DateTime으로 분할 되는 외부 테이블입니다. 아티팩트는 정의 된 경로 아래에 있는 "yyyy/MM/dd" 형식의 디렉터리에 상주 합니다.

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
   folder = "ExternalTables"
)  
```

"Year = yyyy/month = MM/day = dd" 디렉터리 형식의 dateTime으로 분할 된 외부 테이블:

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
   folder = "ExternalTables"
)
```

매월 데이터 파티션이 있는 외부 테이블 및 "yyyy/MM"의 디렉터리 형식:

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
   folder = "ExternalTables"
)
```

두 개의 파티션이 있는 외부 테이블 디렉터리 구조는 두 파티션 (형식이 지정 된 CustomerName 뒤에 기본 날짜/시간 형식)의 연결입니다. 예: "CustomerName = 부드러운 works/2011/11/11":

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

|TableName|TableType|폴더|DocString|속성|ConnectionStrings|파티션|
|---|---|---|---|---|---|---|
|ExternalMultiplePartitions|Blob|ExternalTables|Docs|{"Format": "Csv", "압축": false, "CompressionType": null, "FileExtension": "Csv", "IncludeHeaders": "None", "Encoding": null, "NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName ={0}", "ColumnName": "customername", "Ordinal": 0}, PartitionBy ":" 1.00:00:00 "," ColumnName ":" Timestamp "," Ordinal ": 1}]|

#### <a name="artifact-filtering-logic"></a>아티팩트 필터링 논리

외부 테이블을 쿼리할 때 쿼리 엔진은 관련이 없는 외부 저장소 아티팩트 (blob)를 필터링 하 여 쿼리 성능을 향상 시킵니다. Blob을 반복 하 고 blob을 처리 해야 하는지 여부를 결정 하는 프로세스는 아래에 설명 되어 있습니다.

1. Blob이 있는 위치를 나타내는 URI 패턴을 작성 합니다. 처음에 URI 패턴은 외부 테이블 정의의 일부로 제공 되는 연결 문자열과 같습니다. 파티션이 정의 되어 있으면 URI 패턴에 추가 됩니다.
예를 들어 연결 문자열이이 `https://storageaccount.blob.core.windows.net/container1` 고 datetime 파티션이 정의 `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)`된 경우 해당 URI 패턴 `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd`은입니다 .이 패턴과 일치 하는 위치에서 blob을 찾을 것입니다.
추가 문자열 파티션이 `"CustomerId" customerId` 정의 된 경우 해당 URI 패턴은 `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*`, 등입니다.

2. 만든 URI 패턴 아래에 있는 모든 *직접* blob에 대해 다음을 확인 합니다.

 * 파티션 값이 쿼리에 사용 된 조건자와 일치 합니다.
 * 이러한 속성이 정의 된 `NamePrefix`경우 Blob 이름은로 시작 합니다.
 * 이러한 속성이 정의 된 `FileExtension`경우 Blob 이름이로 끝납니다.

모든 조건을 충족 하면 쿼리 엔진에서 blob을 페치 하 고 처리 합니다.

#### <a name="spark-virtual-columns-support"></a>Spark 가상 열 지원

Spark에서 데이터를 내보내면 데이터 프레임 writer의 `partitionBy` 메서드에 지정 된 파티션 열이 데이터 파일에 기록 되지 않습니다. 이는 데이터가 "폴더" 이름 (예: `column1=<value>/column2=<value>/`)에 이미 존재 하 고 Spark에서 읽을 때이를 인식할 수 있으므로 데이터 중복을 방지 하기 위해 수행 됩니다. 그러나 Kusto를 사용 하려면 파티션 열이 데이터 자체에 있어야 합니다. Kusto의 가상 열에 대 한 지원이 예정 되어 있습니다. 그때까지 다음 해결 방법을 사용 하세요. Spark에서 데이터를 내보낼 때 데이터 프레임를 쓰기 전에 데이터를 분할 하는 모든 열의 복사본을 만듭니다.

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto 외부 테이블을 정의 하는 경우 다음 예제와 같이 파티션 열을 지정 합니다.

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

### <a name="show-external-table-artifacts"></a>. 외부 테이블 아티팩트 표시

* 지정 된 외부 테이블을 쿼리할 때 처리 되는 모든 아티팩트의 목록을 반환 합니다.
* [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show``external` *TableName* TableName `table``artifacts`

**출력**

| 출력 매개 변수 | 형식   | 설명                       |
|------------------|--------|-----------------------------------|
| URI              | string | 외부 저장소 아티팩트의 URI |

**예:**

```kusto
.show external table T artifacts
```

**출력**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

### <a name="create-external-table-mapping"></a>. 외부 테이블 매핑 만들기

`.create``external` `mapping` *MappingName* *MappingInJsonFormat* Externaltablename `json` MappingName MappingInJsonFormat *ExternalTableName* `table`

새 매핑을 만듭니다. 자세한 내용은 [데이터 매핑](./mappings.md#json-mapping)을 참조 하세요.

**예제** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 이름     | 종류 | 매핑                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "int", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

### <a name="alter-external-table-mapping"></a>. 외부 테이블 매핑 변경

`.alter``external` `mapping` *MappingName* *MappingInJsonFormat* Externaltablename `json` MappingName MappingInJsonFormat *ExternalTableName* `table`

기존 매핑을 변경 합니다. 
 
**예제** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 이름     | 종류 | 매핑                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

### <a name="show-external-table-mappings"></a>. 외부 테이블 매핑 표시

`.show``external` `mapping` *MappingName* *ExternalTableName* Externaltablename `json` MappingName `table` 

`.show``external` `json` *ExternalTableName* Externaltablename `table``mappings`

매핑을 표시 합니다 (모두 또는 이름으로 지정 된 하나).
 
**예제** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**예제 출력**

| 이름     | 종류 | 매핑                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

### <a name="drop-external-table-mapping"></a>. 외부 테이블 매핑 삭제

`.drop``external` `mapping` *MappingName* *ExternalTableName* Externaltablename `json` MappingName `table` 

데이터베이스에서 매핑을 삭제 합니다.
 
**예제** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>외부 SQL 테이블

### <a name="create-or-alter-external-sql-table"></a>. 외부 sql 테이블을 만들거나 변경 합니다.

명령이 실행 되는 데이터베이스에서 외부 SQL 테이블을 만들거나 변경 합니다.  

**구문**

`.create` | (`.alter`) `external` TableName ([columnName: columnType], ...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*SqlServerConnectionString*`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* 설명서] [`,` FolderName], property_name 값 ... `=` `)`]


**변수의**

* *TableName* -외부 테이블 이름입니다. [엔터티 이름](../query/schema-entities/entity-names.md)규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *Sqltablename* -SQL 테이블의 이름입니다.
* *SqlServerConnectionString* -SQL server에 대 한 연결 문자열입니다. 다음 중 하나일 수 있습니다. 
    * **Aad 통합 인증** (`Authentication="Active Directory Integrated"`): 사용자 또는 응용 프로그램은 aad를 통해 kusto를 인증 하 고, 동일한 토큰을 사용 하 여 SQL Server 네트워크 끝점에 액세스 합니다.
    * **사용자 이름/암호 인증** (`User ID=...; Password=...;`) [연속 내보내기](data-export/continuous-data-export.md)에 외부 테이블을 사용 하는 경우이 메서드를 사용 하 여 인증을 수행 해야 합니다. 

> [!WARNING]
> 기밀 정보를 포함 하는 연결 문자열 및 쿼리는 모든 Kusto 추적에서 생략 되도록 난독 처리 되어야 합니다. 자세한 내용은 난독 처리 되는 [문자열 리터럴](../query/scalar-data-types/string.md#obfuscated-string-literals) 을 참조 하세요.

**선택적 속성**
| 속성            | Type            | 설명                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 테이블의 폴더입니다.                  |
| `docString`         | `string`        | 테이블을 문서화 하는 문자열입니다.      |
| `firetriggers`      | `true`/`false`  | 인 `true`경우 대상 시스템에서 SQL 테이블에 정의 된 삽입 트리거를 발생 시 정하도록 지시 합니다. 기본값은 `false`입니다. 자세한 내용은 [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) 및 [SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)를 참조 하세요. |
| `createifnotexists` | `true`/ `false` | 인 `true`경우 대상 SQL 테이블이 아직 없으면 생성 됩니다. 이 `primarykey` 경우 기본 키 인 결과 열을 표시 하려면 속성을 제공 해야 합니다. 기본값은 `false`입니다.  |
| `primarykey`        | `string`        | 가 `createifnotexists` 인 `true`경우이 명령으로 생성 되는 경우 SQL 테이블의 기본 키로 사용 될 결과의 열 이름을 나타냅니다.                  |

> [!NOTE]
> * 테이블이 있으면 오류가 발생 하 여 `.create` 명령이 실패 합니다. 기존 `.alter` 테이블을 수정 하는 데 사용 합니다. 
> * 외부 SQL 테이블의 스키마 또는 형식 변경은 지원 되지 않습니다. 

`.create` 에 대 한 [데이터베이스 사용자 권한](../management/access-control/role-based-authorization.md) 및에 대 한 `.alter` [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다. 
 
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

| TableName   | TableType | 폴더         | DocString | 속성                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ExternalSql | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server = tcp: myserver. net.tcp, 1433; Authentication = Active Directory Integrated; 초기 카탈로그 = mydatabase; ",<br>  "FireTriggers": true,<br>  "CreateIfNotExists": true,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>SQL 유형의 외부 테이블 쿼리 
외부 테이블을 쿼리 하는 다른 유형의 외부 테이블에 대 한 Similarl 지원 됩니다. [외부 테이블 쿼리](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)를 참조 하세요. SQL 외부 테이블 쿼리 구현은 SQL 테이블에서 전체 ' SELECT * ' (또는 관련 열 선택)를 실행 하지만 나머지 쿼리는 Kusto 쪽에서 실행 됩니다. 다음 외부 테이블 쿼리를 살펴보십시오. 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto는 SQL database에 대 한 ' SELECT * from TABLE ' 쿼리를 실행 한 다음, Kusto side의 수를 계산 합니다. 이러한 경우에는 외부 테이블 함수를 사용 하는 대신 T-sql을 사용 하 여 직접 작성 하는 경우 (' SELECT COUNT (1) FROM TABLE ') 및 [sql_request 플러그 인](../query/sqlrequestplugin.md)을 사용 하 여 실행 하는 경우 성능이 향상 될 것으로 예상 됩니다. 마찬가지로 필터는 SQL 쿼리로 푸시되 지 않습니다.  
Kusto side에서 추가 실행을 위해 쿼리에서 전체 테이블 또는 관련 열을 읽어야 하는 경우 외부 테이블을 사용 하 여 SQL 테이블을 쿼리 하는 것이 좋습니다. T-sql에서 SQL 쿼리를 크게 최적화할 수 있는 경우 [sql_request 플러그 인](../query/sqlrequestplugin.md)을 사용 합니다.
