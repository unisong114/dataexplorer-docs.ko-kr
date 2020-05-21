---
title: 데이터 매핑-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 매핑을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: c4a64db6d1103aa2a004b816969ab73c7ba19943
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722068"
---
# <a name="data-mappings"></a>데이터 매핑

수집 하는 동안 데이터 매핑을 사용 하 여 들어오는 데이터를 Kusto 테이블 내의 열에 매핑합니다.

Kusto는 `row-oriented` (CSV, JSON 및 AVRO)와 같은 다양 한 유형의 매핑을 지원 하 고 `column-oriented` (Parquet).

매핑 목록의 각 요소는 다음 세 가지 속성을 통해 생성 됩니다.

|속성|Description|
|----|--|
|`column`|Kusto 테이블의 대상 열 이름|
|`datatype`| 필드 Kusto 테이블에 매핑된 열이 없는 경우 해당 열을 만드는 데 사용 하는 데이터 형식|
|`Properties`|필드 아래 각 섹션에서 설명한 대로 각 매핑에 대 한 속성을 포함 하는 속성 모음입니다.


모든 매핑은 [미리 생성](create-ingestion-mapping-command.md) 될 수 있으며, 매개 변수를 사용 하 여 수집 명령에서 참조할 수 있습니다 `ingestionMappingReference` .

## <a name="csv-mapping"></a>CSV 매핑

원본 파일이 CSV (또는 구분 기호가 구분 형식)이 고 해당 스키마가 현재 Kusto 테이블 스키마와 일치 하지 않는 경우 CSV 매핑은 파일 스키마에서 Kusto 테이블 스키마로 매핑됩니다. 테이블이 Kusto에 존재 하지 않는 경우이 매핑에 따라 생성 됩니다. 매핑의 일부 필드가 테이블에 없으면 추가 됩니다. 

CSV 매핑은 모든 구분 기호로 분리 된 형식 (CSV, TSV, PSV, SCSV 및 SOHsv)에 적용할 수 있습니다.

목록의 각 요소는 특정 열에 대 한 매핑을 설명 하 고 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`ordinal`|CSV의 열 순서 번호|
|`constantValue`|필드 CSV 내의 일부 값 대신 열에 사용할 상수 값입니다.|

> [!NOTE]
> `Ordinal`및 `ConstantValue` 는 함께 사용할 수 없습니다.

### <a name="example-of-the-csv-mapping"></a>CSV 매핑의 예

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

* 위의 매핑을 [미리 만든](create-ingestion-mapping-command.md) 경우 제어 명령에서 참조할 수 있습니다 `.ingest` .

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

**참고:** 속성 모음이 없는 다음 매핑 형식은 `Properties` 더 이상 사용 되지 않습니다.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>JSON 매핑

원본 파일이 JSON 형식이 면 파일 내용이 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식이 지정 되지 않은 경우이 테이블은 Kusto 데이터베이스에 있어야 합니다. 모든 비 기존 열에 대해 datatype을 지정 하지 않으면 JSON 매핑에서 매핑된 열이 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대 한 매핑을 설명 하 고 다음 속성을 포함할 수 있습니다. 

|속성|Description|
|----|--|
|`path`|If로 시작 하는 경우 `$` json 문서에서 열의 내용이 될 필드의 json 경로 (전체 문서를 나타내는 json 경로 `$` )입니다. 값이로 시작 하지 않으면 `$` 상수 값이 사용 됩니다.|
|`transform`|필드 [매핑 변형을](#mapping-transformations)사용 하 여 콘텐츠에 적용 해야 하는 변환입니다.|

### <a name="example-of-json-mapping"></a>JSON 매핑 예제

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**참고:** 속성 모음이 없는 다음 매핑 형식은 `Properties` 더 이상 사용 되지 않습니다.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>Avro 매핑

원본 파일이 Avro 형식이 면 Avro 파일 콘텐츠가 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식이 지정 되지 않은 경우이 테이블은 Kusto 데이터베이스에 있어야 합니다. 존재 하지 않는 모든 열에 대해 datatype을 지정 하지 않으면 Avro 매핑에서 매핑된 열이 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대 한 매핑을 설명 하 고 다음 속성을 포함할 수 있습니다. 

|속성|Description|
|----|--|
|`Field`|Avro 레코드의 필드 이름입니다.|
|`Path`|를 사용 하 `field` 는 대신, 필요한 경우 Avro 레코드 필드의 내부 부분을 사용할 수 있습니다. 값은 레코드의 루트에서의 JSON 경로를 나타냅니다. 자세한 내용은 아래 참고를 참조 하세요. |
|`transform`|필드 [지원 되는 변환이](#mapping-transformations)있는 콘텐츠에 적용 해야 하는 변환입니다.|

**참고 사항**
>[!NOTE]
> * `field`및 `path` 는 함께 사용할 수 없으며 하나만 사용할 수 있습니다. 
> * `path`루트를 가리킬 수 없습니다 `$` . 경로 수준이 하나 이상 있어야 합니다.

아래 두 가지 대안은 동일 합니다.

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>AVRO 매핑의 예

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**참고:** 속성 모음이 없는 다음 매핑 형식은 `Properties` 더 이상 사용 되지 않습니다.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>Parquet 매핑

원본 파일이 Parquet 형식이 면 파일 내용이 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식이 지정 되지 않은 경우이 테이블은 Kusto 데이터베이스에 있어야 합니다. 모든 비 기존 열에 대해 datatype을 지정 하지 않으면 Parquet 매핑에서 매핑된 열이 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대 한 매핑을 설명 하 고 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`path`|If로 시작 하는 경우 `$` Parquet 문서에서 열의 내용이 될 필드의 json 경로 (전체 문서를 나타내는 json 경로 `$` )입니다. 값이로 시작 하지 않으면 `$` 상수 값이 사용 됩니다.|
|`transform`|필드 콘텐츠에 적용 해야 하는 [매핑 변환](#mapping-transformations) 입니다.


### <a name="example-of-the-parquet-mapping"></a>Parquet 매핑의 예

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

* 위의 매핑을 [미리 만든](create-ingestion-mapping-command.md) 경우 제어 명령에서 참조할 수 있습니다 `.ingest` .

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Orc 매핑

원본 파일이 Orc 형식이 면 파일 내용이 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식이 지정 되지 않은 경우이 테이블은 Kusto 데이터베이스에 있어야 합니다. 모든 비 기존 열에 대해 datatype을 지정 하지 않으면 Orc 매핑에서 매핑된 열이 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대 한 매핑을 설명 하 고 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`path`|If로 시작 하는 경우 `$` Orc 문서에서 열의 내용이 될 필드의 json 경로 (전체 문서를 나타내는 json 경로 `$` )입니다. 값이로 시작 하지 않으면 `$` 상수 값이 사용 됩니다.|
|`transform`|필드 콘텐츠에 적용 해야 하는 [매핑 변환](#mapping-transformations) 입니다.

### <a name="example-of-orc-mapping"></a>Orc 매핑의 예

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 위의 매핑이 제어 명령의 일부로 제공 되는 경우 `.ingest` JSON 문자열로 serialize 됩니다.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>매핑 매핑

일부 데이터 형식 매핑 (Parquet, JSON 및 Avro)은 간단 하 고 유용한 수집 시간 변환을 지원 합니다. 수집 시 보다 복잡 한 처리를 필요로 하는 시나리오의 경우 KQL 식을 사용 하 여 간단한 처리를 정의할 수 있는 [업데이트 정책](update-policy.md)을 사용 합니다.

|경로 종속 변환|Description|조건|
|--|--|--|
|`PropertyBagArrayToDictionary`|속성의 JSON 배열 (예: {events: [{"n1": "v1"}, {"n2": "v2"}]})을 사전으로 변환 하 고 유효한 JSON 문서 (예: {"n1": "v1", "n2": "v2"})로 serialize 합니다.|을 사용 하는 경우에만 적용할 수 있습니다. `path`|
|`GetPathElement(index)`|지정 된 인덱스에 따라 지정 된 경로에서 요소를 추출 합니다 (예: 경로: $. b., GetPathElement (0) = = "c", GetPathElement (-1) = = "b", 형식 문자열).|을 사용 하는 경우에만 적용할 수 있습니다. `path`|
|`SourceLocation`|데이터를 제공한 저장소 아티팩트의 이름, 형식 문자열 (예: blob의 "BaseUri" 필드)입니다.|
|`SourceLineNumber`|해당 저장소 아티팩트에 상대적인 오프셋입니다. long (' 1 '부터 시작 하 여 새 레코드 당 증가)을 입력 합니다.|
|`DateTimeFromUnixSeconds`|Unix 시간 (1970-01-01 이후 초)을 나타내는 숫자를 UTC datetime 문자열로 변환 합니다.|
|`DateTimeFromUnixMilliseconds`|Unix 시간 (1970-01-01 이후 밀리초)을 나타내는 숫자를 UTC 날짜/시간 문자열로 변환 합니다.|
|`DateTimeFromUnixMicroseconds`|Unix 시간 (1970-01-01 이후 마이크로초)을 나타내는 숫자를 UTC 날짜/시간 문자열로 변환 합니다.|
|`DateTimeFromUnixNanoseconds`|Unix 시간 (1970-01-01 이후 시간 나노초)을 나타내는 숫자를 UTC datetime 문자열로 변환 합니다.|
