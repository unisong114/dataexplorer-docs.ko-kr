---
title: Azure Storage 또는 Azure Data Lake의 외부 테이블-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: db99d1d46c321bff0f5d7b370766900ea7d1d5a0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227726"
---
# <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage 또는 Azure Data Lake의 외부 테이블

다음 명령은 외부 테이블을 만드는 방법을 설명 합니다. 테이블은 Azure Blob Storage, Azure Data Lake Store Gen1 또는 Azure Data Lake Store Gen2에서 찾을 수 있습니다. 
[저장소 연결 문자열](../api/connection-strings/storage.md) 에서는 이러한 각 옵션에 대 한 연결 문자열을 만드는 방법을 설명 합니다. 

## <a name="create-or-alter-external-table"></a>. create 또는. alter external table

**구문**

( `.create`  |  `.alter` ) `external` `table` *TableName* (*스키마*)  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *Partition* [ `,` ...]]  
`dataformat``=` *형식*  
`(`  
*StorageConnectionString* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*], *property_name* `=` *값*... `,` `)` ]

명령이 실행 되는 데이터베이스에서 새 외부 테이블을 만들거나 변경 합니다.

**매개 변수**

* *TableName* -외부 테이블 이름입니다. [엔터티 이름](../query/schema-entities/entity-names.md)규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *스키마* -형식의 외부 데이터 스키마: `ColumnName:ColumnType[, ColumnName:ColumnType ...]` . 외부 데이터 스키마를 알 수 없는 경우에는 외부 파일 내용에 따라 스키마를 유추할 수 있는 [infer_storage_schema](../query/inferstorageschemaplugin.md) 플러그 인을 사용 합니다.
* *파티션* -하나 또는 여러 파티션 정의 (옵션). 아래의 파티션 구문을 참조 하세요.
* *Format* -데이터 형식입니다. 수집 [형식은](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) 쿼리를 위해 지원 됩니다. [내보내기 시나리오](data-export/export-data-to-an-external-table.md) 에 외부 테이블을 사용 하는 것은,, `CSV` `TSV` `JSON` , 형식으로 `Parquet` 제한 됩니다.
* *StorageConnectionString* -자격 증명을 포함 하 여 Blob 컨테이너 또는 Azure Data Lake Store 파일 시스템 (가상 디렉터리 또는 폴더)을 Azure Blob Storage 하는 하나 또는 여러 경로입니다. 자세한 내용은 [저장소 연결 문자열](../api/connection-strings/storage.md) 을 참조 하세요. 많은 양의 데이터를 외부 테이블로 [내보내는](data-export/export-data-to-an-external-table.md) 동안 저장소 제한을 방지 하기 위해 단일 저장소 계정을 제공 합니다. 내보내기는 제공 된 모든 계정 간에 쓰기를 배포 합니다. 

**파티션 구문**

[ `format_datetime =` *DateTimePartitionFormat*] `bin(` *TimestampColumnName*, *파티션*`)`  
|   
[*Stringformatprefix*] *Stringcolumnname* [*stringformatsuffix*])

**파티션 매개 변수**

* *DateTimePartitionFormat* -출력 경로에서 필요한 디렉터리 구조의 형식입니다 (옵션). 분할이 정의 되 고 형식이 지정 되지 않은 경우 기본값은 "yyyy/MM/dd/HH/mm"입니다. 이 형식은 파티션 (파티션)을 기반으로 합니다. 예를 들어 1d로 분할 하는 경우 structure는 "yyyy/MM/dd"입니다. 1 h로 분할 하는 경우 구조체는 "yyyy/MM/dd/HH"가 됩니다.
* 테이블이 분할 된 *TimestampColumnName* -Datetime 열입니다. 외부 테이블로 내보낼 때 타임 스탬프 열은 외부 테이블 스키마 정의 및 내보내기 쿼리의 출력에 있어야 합니다.
* Partition *bytimespan* -분할할 timespan 리터럴입니다.
* *Stringformatprefix* -아티팩트 경로의 일부가 되 고, 테이블 값 앞에 연결 된 상수 문자열 리터럴입니다 (옵션).
* *Stringformatsuffix* -아티팩트 경로의 일부가 되 고 테이블 값 (선택 사항) 다음에 연결 되는 상수 문자열 리터럴입니다.
* *Stringcolumnname* -테이블이 분할 된 문자열 열입니다. 문자열 열은 외부 테이블 스키마 정의에 있어야 합니다.

**선택적 속성**:

| 속성         | 유형     | 설명       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 테이블의 폴더                                                                     |
| `docString`      | `string` | 테이블을 문서화 하는 문자열                                                       |
| `compressed`     | `bool`   | 설정 하는 경우 blob이 파일로 압축 되는지 여부를 나타냅니다. `.gz`                  |
| `includeHeaders` | `string` | CSV 또는 TSV blob의 경우 blob에 헤더가 포함 되는지 여부를 나타냅니다.                     |
| `namePrefix`     | `string` | 설정 하는 경우 blob의 접두사를 나타냅니다. 쓰기 작업 시 모든 blob이이 접두사로 작성 됩니다. 읽기 작업에서는이 접두사가 있는 blob만 읽습니다. |
| `fileExtension`  | `string` | 설정 하는 경우 blob의 파일 확장명을 나타냅니다. 쓰기에서는 blob 이름이이 접미사로 종료 됩니다. 읽을 때이 파일 확장명을 가진 blob만 읽습니다.           |
| `encoding`       | `string` | 텍스트를 인코딩하는 방법을 나타냅니다. `UTF8NoBOM` (기본값) 또는 `UTF8BOM` 입니다.             |

쿼리의 외부 테이블 매개 변수에 대 한 자세한 내용은 [아티팩트 필터링 논리](#artifact-filtering-logic)를 참조 하세요.

> [!NOTE]
> * 테이블이 있으면 `.create` 오류가 발생 하 여 명령이 실패 합니다. `.alter`기존 테이블을 수정 하는 데 사용 합니다. 
> * 외부 blob 테이블의 스키마, 형식 또는 파티션 정의 변경은 지원 되지 않습니다. 

에 대 한 [데이터베이스 사용자 권한](../management/access-control/role-based-authorization.md) `.create` 및에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 `.alter` 합니다. 
 
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

DateTime으로 분할 되는 외부 테이블입니다. 아티팩트는 정의 된 경로 아래에 있는 "yyyy/MM/dd" 형식의 디렉터리에 있습니다.

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

두 개의 파티션이 있는 외부 테이블 디렉터리 구조는 두 파티션 (형식이 지정 된 CustomerName 뒤에 기본 날짜/시간 형식)의 연결입니다. 예: "CustomerName = 소프트 works/2011/11/11":

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
|ExternalMultiplePartitions|Blob|ExternalTables|Docs|{"Format": "Csv", "압축": false, "CompressionType": null, "FileExtension": "Csv", "IncludeHeaders": "None", "Encoding": null, "NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName = {0} ", "ColumnName": "customername", "Ordinal": 0}, PartitionBy ":" 1.00:00:00 "," ColumnName ":" Timestamp "," Ordinal ": 1}]|

### <a name="artifact-filtering-logic"></a>아티팩트 필터링 논리

외부 테이블을 쿼리할 때 쿼리 엔진은 관련이 없는 외부 저장소 아티팩트 (blob)를 필터링 하 여 성능을 향상 시킵니다. Blob을 반복 하 고 blob을 처리 해야 하는지 여부를 결정 하는 프로세스는 아래에 설명 되어 있습니다.

1. Blob이 있는 위치를 나타내는 URI 패턴을 작성 합니다. 처음에 URI 패턴은 외부 테이블 정의의 일부로 제공 되는 연결 문자열과 같습니다. 파티션이 정의 되어 있으면 URI 패턴에 추가 됩니다.
예를 들어 연결 문자열이이 `https://storageaccount.blob.core.windows.net/container1` 고 datetime 파티션이 정의 된 경우 `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` 해당 URI 패턴은 `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` 입니다 .이 패턴과 일치 하는 위치에서 blob을 찾을 것입니다.
추가 문자열 파티션이 정의 된 경우 `"CustomerId" customerId` 해당 URI 패턴은 `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` 입니다.

1. 만든 URI 패턴 아래에 있는 모든 *직접* blob에 대해 다음을 확인 합니다.

   * 파티션 값이 쿼리에 사용 된 조건자와 일치 합니다.
   * 이러한 속성이 정의 된 경우 Blob 이름은로 시작 `NamePrefix` 합니다.
   * 이러한 속성이 정의 된 경우 Blob 이름이로 끝납니다 `FileExtension` .

모든 조건을 충족 하면 쿼리 엔진에서 blob을 페치 하 고 처리 합니다.

### <a name="spark-virtual-columns-support"></a>Spark 가상 열 지원

Spark에서 데이터를 내보내면 데이터 프레임 writer의 메서드에 지정 된 파티션 열 `partitionBy` 이 데이터 파일에 기록 되지 않습니다. 이 프로세스는 "폴더" 이름에 이미 있는 데이터가 있으므로 데이터 중복을 방지 합니다. 예를 들어, `column1=<value>/column2=<value>/` 및 Spark는 읽을 때이를 인식할 수 있습니다. 그러나 Kusto를 사용 하려면 파티션 열이 데이터 자체에 있어야 합니다. Kusto의 가상 열에 대 한 지원이 예정 되어 있습니다. 그때까지 다음 해결 방법을 사용 하세요. Spark에서 데이터를 내보낼 때 데이터 프레임를 쓰기 전에 데이터를 분할 하는 모든 열의 복사본을 만듭니다.

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto에서 외부 테이블을 정의 하는 경우 다음 예제와 같이 파티션 열을 지정 합니다.

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

## <a name="show-external-table-artifacts"></a>. 외부 테이블 아티팩트 표시

* 지정 된 외부 테이블을 쿼리할 때 처리 되는 모든 아티팩트의 목록을 반환 합니다.
* [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show``external` `table` *TableName*`artifacts`

**출력**

| 출력 매개 변수 | 유형   | 설명                       |
|------------------|--------|-----------------------------------|
| URI              | 문자열 | 외부 저장소 아티팩트의 URI |

**예:**

```kusto
.show external table T artifacts
```

**출력**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>. 외부 테이블 매핑 만들기

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

새 매핑을 만듭니다. 자세한 내용은 [데이터 매핑](./mappings.md#json-mapping)을 참조 하세요.

**예제** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 속성     | 종류 | 매핑                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "int", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>. 외부 테이블 매핑 변경

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

기존 매핑을 변경 합니다. 
 
**예제** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**예제 출력**

| 속성     | 종류 | 매핑                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>. 외부 테이블 매핑 표시

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Externaltablename `json``mappings`

매핑을 표시 합니다 (모두 또는 이름으로 지정 된 하나).
 
**예제** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**예제 출력**

| 속성     | 종류 | 매핑                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>. 외부 테이블 매핑 삭제

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

데이터베이스에서 매핑을 삭제 합니다.
 
**예제** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
