---
title: Azure Storage 또는 Azure Data Lake에서 외부 테이블 만들기 및 변경-Azure 데이터 탐색기
description: 이 문서에서는 Azure Storage 또는 Azure Data Lake에서 외부 테이블을 만들고 변경 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: df38761d7ffebdf5e36c14ea25b0d02377bfa128
ms.sourcegitcommit: fdc1f917621e9b7286bba23903101298cccc4c95
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93364125"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage 또는 Azure Data Lake의 외부 테이블 만들기 및 변경

다음 명령은 Azure Blob Storage, Azure Data Lake Store Gen1 또는 Azure Data Lake Store Gen2에 있는 외부 테이블을 만드는 방법을 설명 합니다. 

외부 Azure Storage 테이블 기능에 대 한 소개는 [Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터 쿼리](../../data-lake-query-data.md)를 참조 하세요.

## <a name="create-or-alter-external-table"></a>. create 또는. alter external table

**구문**

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *[TableName](#table-name)* `(` *[스키마](#schema)*`)`  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` `(` *[파티션](#partitions)* `)` [ `pathformat` `=` `(` *[pathformat](#path-format)* `)` ]]  
`dataformat``=` *[형식](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[값](#properties)* `,` ... `)` ]  

명령이 실행 되는 데이터베이스에서 새 외부 테이블을 만들거나 변경 합니다.

> [!NOTE]
> * 테이블이 있으면 `.create` 오류가 발생 하 여 명령이 실패 합니다. `.create-or-alter` `.alter` 기존 테이블을 수정 하려면 또는를 사용 합니다.
> * 외부 blob 테이블의 스키마, 형식 또는 파티션 정의 변경은 지원 되지 않습니다. 
> * 이 작업을 수행 하려면에 대 한 [데이터베이스 사용자 권한과](../management/access-control/role-based-authorization.md) `.create` 에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다 `.alter` . 

**매개 변수**

<a name="table-name"></a>
*TableName*

[엔터티 이름](../query/schema-entities/entity-names.md) 규칙을 준수 하는 외부 테이블 이름입니다.
외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.

<a name="schema"></a>
*스키마*

외부 데이터 스키마는 다음 형식을 사용 하 여 설명 됩니다.

&nbsp;&nbsp;*ColumnName* `:` *ColumnType* [ `,` *ColumnName* `:` *ColumnType* ]

여기서 *ColumnName* 은 [엔터티 명명](../query/schema-entities/entity-names.md) 규칙을 준수 하 고 *ColumnType* 는 지원 되는 [데이터 형식](../query/scalar-data-types/index.md)중 하나입니다.

> [!TIP]
> 외부 데이터 스키마를 알 수 없는 경우에는 [유추 \_ 저장소 \_ 스키마](../query/inferstorageschemaplugin.md) 플러그 인을 사용 합니다. 그러면 외부 파일 콘텐츠를 기반으로 스키마를 유추할 수 있습니다.

<a name="partitions"></a>
*영역*

외부 테이블을 분할 하는 데 사용할 쉼표로 구분 된 열 목록입니다. 파티션 열은 데이터 파일 자체 또는 파일 경로의 sa 부분에 있을 수 있습니다 ( [가상 열](#virtual-columns)에 대해 자세히 읽음).

파티션 목록은 다음 형식 중 하나를 사용 하 여 지정 된 파티션 열의 조합입니다.

* 파티션 ( [가상 열](#virtual-columns)을 나타냄)

  *PartitionName* `:` (`datetime` | `string`)

* String 열 값을 기반으로 하는 파티션입니다.

  *PartitionName* `:` `string` `=` *ColumnName*

* 파티션, 문자열 열 값 [해시](../query/hashfunction.md), 나머지 *숫자* 를 기반으로 합니다.

  *PartitionName* `:` `long` `=` `hash` `(` *ColumnName* `,` *번호*`)`

* 분할 된 날짜/시간 열의 잘린 값을 기반으로 합니다. [Startofyear](../query/startofyearfunction.md), [startofmonth](../query/startofmonthfunction.md), [startofweek](../query/startofweekfunction.md), [startofday](../query/startofdayfunction.md) 또는 [bin](../query/binfunction.md) 함수에 대 한 설명서를 참조 하세요.

  *PartitionName* `:` `datetime` `=` ( `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ) `(` *ColumnName*`)`  
  *PartitionName* `:` `datetime` `=` `bin` `(` *ColumnName* `,` *TimeSpan*`)`

분할 정의 정확성을 확인 하려면 `sampleUris` 외부 테이블을 만들 때 속성을 사용 합니다.

<a name="path-format"></a>
*PathFormat*

분할 외에도 지정할 수 있는 외부 데이터 URI 파일 경로 형식입니다. 경로 형식은 파티션 요소 및 텍스트 구분 기호의 시퀀스입니다.

&nbsp;&nbsp;[ *Stringseparator* ] *파티션* [ *stringseparator* ] [ *파티션* [ *stringseparator* ] ...]  

여기서 *partition* 은 절에 선언 된 파티션을 참조 하 `partition` `by` 고 *stringseparator* 는 따옴표로 묶인 텍스트입니다. 연속 된 파티션 요소는 *Stringseparator* 를 사용 하 여 별도로 설정 해야 합니다.

원래 파일 경로 접두사는 문자열로 렌더링 되 고 해당 텍스트 구분 기호로 구분 된 파티션 요소를 사용 하 여 생성할 수 있습니다. Datetime 파티션 값을 렌더링 하는 데 사용 되는 형식을 지정 하려면 다음 매크로를 사용할 수 있습니다.

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* `,` *PartitionName*`)`  

여기서 *DateTimeFormat* 는 형식 지정자를 중괄호로 묶을 수 있는 확장명이 있는 .net 형식 사양을 따릅니다. 예를 들어 다음 두 가지 형식은 동일 합니다.

&nbsp;&nbsp;`'year='yyyy'/month='MM` 하거나 `year={yyyy}/month={MM}`

기본적으로 datetime 값은 다음 형식을 사용 하 여 렌더링 됩니다.

| 파티션 함수    | 기본 형식 |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*열의*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*열의*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*열의*`, 1m)` | `yyyy/MM/dd/HH/mm` |

*Pathformat* 이 외부 테이블 정의에서 생략 된 경우에는 정확 하 게 정의 된 순서 대로 모든 파티션이 구분 기호를 사용 하 여 구분 됩니다 `/` . 파티션은 기본 문자열 표현을 사용 하 여 렌더링 됩니다.

경로 형식 정의 정확성을 확인 하려면 `sampleUris` 외부 테이블을 만들 때 속성을 사용 합니다.

<a name="format"></a>
*형식과*

수집 [형식](../../ingestion-supported-formats.md)에 해당 하는 데이터 형식입니다.

> [!NOTE]
> [내보내기 시나리오](data-export/export-data-to-an-external-table.md) 에 외부 테이블을 사용 하는 것은 `CSV` , `TSV` `JSON` 및 형식으로 제한 `Parquet` 됩니다.

<a name="connection-string"></a>
*StorageConnectionString*

자격 증명을 포함 하 여 Blob 컨테이너 또는 Azure Data Lake Store 파일 시스템 (가상 디렉터리 또는 폴더)을 Azure Blob Storage 하는 하나 이상의 경로입니다.
자세한 내용은 [저장소 연결 문자열](../api/connection-strings/storage.md) 을 참조 하세요.

> [!TIP]
> 많은 양의 데이터를 외부 테이블로 [내보내는](data-export/export-data-to-an-external-table.md) 동안 저장소 제한을 방지 하기 위해 단일 저장소 계정을 제공 합니다. 내보내기는 제공 된 모든 계정 간에 쓰기를 배포 합니다. 

<a name="properties"></a>
*선택적 속성*

| 속성         | 유형     | Description       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 테이블의 폴더                                                                     |
| `docString`      | `string` | 테이블을 문서화 하는 문자열                                                       |
| `compressed`     | `bool`   | 설정 하는 경우 파일이 파일로 압축 되는지 여부를 나타냅니다 `.gz` ( [내보내기 시나리오](data-export/export-data-to-an-external-table.md) 에서만 사용 됨). |
| `includeHeaders` | `string` | 구분 기호로 분리 된 텍스트 형식 (CSV, TSV, ...)은 파일에 헤더가 포함 되어 있는지 여부를 나타냅니다. 가능한 값은 다음과 같습니다. `All` (모든 파일에 헤더 포함) `FirstFile` . (폴더의 첫 번째 파일에는 헤더가 포함 되어 있음) `None` . |
| `namePrefix`     | `string` | 설정 하는 경우 파일의 접두사를 나타냅니다. 쓰기 작업 시 모든 파일이이 접두사로 작성 됩니다. 읽기 작업에서는이 접두사가 포함 된 파일만 읽습니다. |
| `fileExtension`  | `string` | 설정 하는 경우 파일의 파일 확장명을 나타냅니다. 쓰기에서는 파일 이름이이 접미사로 종료 됩니다. 읽을 때이 파일 확장명을 가진 파일만 읽습니다.           |
| `encoding`       | `string` | 텍스트를 인코딩하는 방법을 나타냅니다. `UTF8NoBOM` (기본값) 또는 `UTF8BOM` 입니다.             |
| `sampleUris`     | `bool`   | 설정 하는 경우 명령 결과는 외부 테이블 정의에 예상 되는 외부 데이터 파일 URI의 몇 가지 예를 제공 합니다 (샘플이 두 번째 결과 테이블에 반환 됨). 이 옵션은 *[파티션](#partitions)* 및 *[pathformat](#path-format)* 매개 변수가 제대로 정의 되었는지 여부를 확인 하는 데 도움이 됩니다. |
| `validateNotEmpty` | `bool`   | 설정 하는 경우 연결 문자열에 콘텐츠를 포함 하기 위해 연결 문자열의 유효성이 검사 됩니다. 지정 된 URI 위치가 존재 하지 않거나 액세스 권한이 부족 한 경우 명령이 실패 합니다. |

> [!TIP]
> `namePrefix` `fileExtension` 쿼리 중 데이터 파일 필터링에서 실행 되는 역할 및 속성에 대 한 자세한 내용은 [파일 필터링 논리](#file-filtering) 섹션을 참조 하세요.
 
<a name="examples"></a>
**예와** 

분할 되지 않은 외부 테이블입니다. 데이터 파일은 정의 된 컨테이너 바로 아래에 배치 되어야 합니다.

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

날짜별로 분할 된 외부 테이블입니다. 날짜 파일은 기본 날짜/시간 형식의 디렉터리에 배치 되어야 합니다 `yyyy/MM/dd` .

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

다음 디렉터리 형식으로 월별로 분할 된 외부 테이블 `year=yyyy/month=MM` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

먼저 고객 이름, 날짜별로 분할 된 외부 테이블입니다. 필요한 디렉터리 구조는 `customer_name=Softworks/2019/02/01` 다음과 같습니다. 예를 들면 다음과 같습니다.

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

처음에는 사용자 이름 해시 (모듈로 10)에 의해 먼저 분할 된 외부 테이블이 날짜별로 분할 됩니다. 필요한 디렉터리 구조는입니다 (예:) `customer_id=5/dt=20190201` . 데이터 파일 이름이 확장명으로 끝납니다 `.txt` .

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

쿼리에서 파티션 열을 기준으로 필터링 하려면 쿼리 조건자에 원래 열 이름을 지정 합니다.

```kusto
external_table("ExternalTable")
 | where Timestamp between (datetime(2020-01-01) .. datetime(2020-02-01))
 | where CustomerName in ("John.Doe", "Ivan.Ivanov")
```

**샘플 출력**

|TableName|TableType|폴더|DocString|속성|ConnectionStrings|파티션|PathFormat|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|ExternalTable|Blob|ExternalTables|Docs|{"Format": "Csv", "압축": false, "CompressionType": null, "FileExtension": null, "IncludeHeaders": "None", "Encoding": null, "NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod": 10, "Name": "CustomerId", "ColumnName": "CustomerName", "Ordinal": 0}, {"Function": "StartOfDay", "Name": "Date", "ColumnName": "Timestamp", "Ordinal": 1}]|"customer \_ id =" CustomerId "/dt =" datetime \_ 패턴 ("yyyyMMdd", Date)|

<a name="virtual-columns"></a>
**가상 열**

Spark에서 데이터를 내보내면 데이터 프레임 writer의 메서드에 지정 된 파티션 열 `partitionBy` 이 데이터 파일에 기록 되지 않습니다. 이 프로세스는 "폴더" 이름에 이미 있는 데이터가 있으므로 데이터 중복을 방지 합니다. 예를 들어, `column1=<value>/column2=<value>/` 및 Spark는 읽을 때이를 인식할 수 있습니다.

외부 테이블은 가상 열을 지정 하기 위해 다음 구문을 지원 합니다.

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

쿼리에서 가상 열을 기준으로 필터링 하려면 쿼리 조건자에서 파티션 이름을 지정 합니다.

```kusto
external_table("ExternalTable")
 | where Date between (datetime(2020-01-01) .. datetime(2020-02-01))
 | where CustomerName in ("John.Doe", "Ivan.Ivanov")
```

<a name="file-filtering"></a>
**파일 필터링 논리**

외부 테이블을 쿼리할 때 쿼리 엔진은 관련이 없는 외부 저장소 파일을 필터링 하 여 성능을 향상 시킵니다. 파일을 반복 하 고 파일을 처리 해야 하는지 여부를 결정 하는 프로세스는 아래에 설명 되어 있습니다.

1. 파일이 있는 위치를 나타내는 URI 패턴을 작성 합니다. 처음에 URI 패턴은 외부 테이블 정의의 일부로 제공 되는 연결 문자열과 같습니다. 파티션이 정의 되어 있으면 *[Pathformat](#path-format)* 을 사용 하 여 렌더링 된 다음 URI 패턴에 추가 됩니다.

2. 만든 URI 패턴 아래에 있는 모든 파일에 대해 다음을 확인 합니다.

   * 파티션 값이 쿼리에 사용 된 조건자와 일치 합니다.
   * 이러한 속성이 정의 된 경우 Blob 이름은로 시작 `NamePrefix` 합니다.
   * 이러한 속성이 정의 된 경우 Blob 이름이로 끝납니다 `FileExtension` .

모든 조건이 충족 되 면 쿼리 엔진에서 파일을 페치 하 고 처리 합니다.

> [!NOTE]
> 초기 URI 패턴은 쿼리 조건자 값을 사용 하 여 빌드됩니다. 이는 닫힌 시간 범위 뿐만 아니라 제한 된 문자열 값 집합에 가장 적합 합니다.

## <a name="show-external-table-artifacts"></a>. 외부 테이블 아티팩트 표시

지정 된 외부 테이블을 쿼리할 때 처리 되는 모든 파일의 목록을 반환 합니다.

> [!NOTE]
> 작업에는 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults* ]

여기서 *MaxResults* 은 선택적 매개 변수 이며 결과 수를 제한 하도록 설정할 수 있습니다.

**출력**

| 출력 매개 변수 | 유형   | 설명                       |
|------------------|--------|-----------------------------------|
| URI              | string | 외부 저장소 데이터 파일의 URI |
| 크기             | long   | 파일 길이 (바이트)              |
| 파티션        | 동적 | 분할 된 외부 테이블에 대 한 파일 파티션을 설명 하는 동적 개체 |

> [!TIP]
> 외부 테이블에서 참조 하는 모든 파일에 대 한 반복은 파일 수에 따라 비용이 많이 들 수 있습니다. `limit`일부 URI 예제를 보려는 경우에는 매개 변수를 사용 해야 합니다.

**예:**

```kusto
.show external table T artifacts
```

**출력:**

| URI                                                                     | 크기 | 파티션 |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


분할 된 테이블의 경우 `Partition` 열에 추출 된 파티션 값이 포함 됩니다.

**출력:**

| URI                                                                     | 크기 | 파티션 |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>. 외부 테이블 매핑 만들기

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

새 매핑을 만듭니다. 자세한 내용은 [데이터 매핑](./mappings.md#json-mapping)을 참조 하세요.

**예제** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**예제 출력**

| 이름     | Kind | 매핑                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$ rowguid"}}] |

## <a name="alter-external-table-mapping"></a>. 외부 테이블 매핑 변경

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

기존 매핑을 변경 합니다. 
 
**예제** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**예제 출력**

| 이름     | Kind | 매핑                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$ rowguid"}}] |

## <a name="show-external-table-mappings"></a>. 외부 테이블 매핑 표시

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Externaltablename `json``mappings`

매핑을 표시 합니다 (모두 또는 이름으로 지정 된 하나).
 
**예제** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**예제 출력**

| 이름     | Kind | 매핑                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$ rowguid"}}] |

## <a name="drop-external-table-mapping"></a>. 외부 테이블 매핑 삭제

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

데이터베이스에서 매핑을 삭제 합니다.
 
**예제** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>다음 단계

* [외부 테이블을 쿼리](../../data-lake-query-data.md)합니다.
* [외부 테이블로 데이터를 내보냅니다](data-export/export-data-to-an-external-table.md).
* [외부 테이블로 데이터를 지속적으로 내보냅니다](data-export/continuous-data-export.md).
