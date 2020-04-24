---
title: 데이터 매핑 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 매핑에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520777"
---
# <a name="data-mappings"></a>데이터 매핑

데이터 매핑은 들어오는 데이터를 Kusto 테이블 내부의 열에 매핑하는 데 사용됩니다.

Kusto는 (CSV, JSON 및 AVRO) 및 `row-oriented` `column-oriented` (마루) 매핑의 다른 유형을 지원합니다.

매핑 목록의 각 요소는 세 가지 속성으로 생성됩니다.

|속성|Description|
|----|--|
|`column`|Kusto 테이블의 대상 열 이름|
|`datatype`| (선택 사항) Kusto 테이블에 아직 없는 경우 매핑된 열을 만들 수 있는 데이터 형식|
|`Properties`|(선택 사항) 아래 각 섹션에 설명된 대로 각 매핑에 특정한 속성을 포함하는 속성 백입니다.


모든 매핑을 [미리 만들](create-ingestion-mapping-command.md) 수 있으며 매개 변수를 사용하여 `ingestionMappingReference` 인제스트 명령에서 참조할 수 있습니다.

## <a name="csv-mapping"></a>CSV 매핑

원본 파일이 CSV(또는 섬망으로 분리된 형식)이고 해당 스키마가 현재 Kusto 테이블 스키마와 일치하지 않는 경우 CSV 매핑 맵이 파일 스키마에서 Kusto 테이블 스키마에 매핑됩니다. Kusto에 테이블이 없으면 이 매핑에 따라 테이블이 만들어집니다. 매핑의 일부 필드가 테이블에 없으면 추가됩니다. 

CSV 매핑은 CSV, TSV, PSV, SCSV 및 SOHsv의 모든 구분식 형식에 적용할 수 있습니다.

목록의 각 요소는 특정 열에 대한 매핑을 설명하며 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`ordinal`|CSV의 열 주문 번호|
|`constantValue`|(선택 사항) CSV 내부의 일부 값 대신 열에 사용할 상수 값|

> [!NOTE]
> `Ordinal``ConstantValue` 상호 배타적입니다.

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
> 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

* 위의 매핑을 [미리 만든](create-ingestion-mapping-command.md) 경우 `.ingest` 제어 명령에서 참조할 수 있습니다.
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

```
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

**참고:** `Properties` 속성 가방이 없는 다음 매핑 형식은 현재 지원되지만 나중에 더 이상 사용되지 않을 수 있습니다.

```
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

원본 파일이 JSON 형식이면 파일 콘텐츠가 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식을 지정하지 않는 한 테이블은 Kusto 데이터베이스에 있어야 합니다. JSON 매핑에 매핑된 열은 모든 비존재하지 않는 열에 대해 데이터 형식을 지정하지 않는 한 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대한 매핑을 설명하며 다음 속성을 포함할 수 있습니다. 

|속성|Description|
|----|--|
|`path`|`$`로 시작하는 경우 : JSON 문서의 열 내용이 될 필드에 대한 JSON 경로(전체 문서를 `$`나타내는 JSON 경로는)입니다. 값이 시작되지 않으면 `$`: 상수 값이 사용됩니다.|
|`transform`|(선택 사항) [매핑 변환을](#mapping-transformations)사용 하 고 콘텐츠에 적용 해야 하는 변환입니다.|

### <a name="example-of-json-mapping"></a>JSON 매핑의 예

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
> 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**참고:** `Properties` 속성 가방이 없는 다음 매핑 형식은 현재 지원되지만 나중에 더 이상 사용되지 않을 수 있습니다.

```
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
    
## <a name="avro-mapping"></a>아브로 매핑

소스 파일이 Avro 형식이면 Avro 파일 콘텐츠가 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식을 지정하지 않는 한 테이블은 Kusto 데이터베이스에 있어야 합니다. Avro 매핑에 매핑된 열은 모든 비존재하지 않는 열에 대해 데이터 형식을 지정하지 않는 한 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대한 매핑을 설명하며 다음 속성을 포함할 수 있습니다. 

|속성|Description|
|----|--|
|`Field`|아브로 레코드의 필드 이름입니다.|
|`Path`|필요한 경우 `field` Avro 레코드 필드의 내부 부분을 사용할 수 있는 사용입니다. 값은 레코드의 루트에서 JSON 경로를 나타냅니다. 자세한 내용은 아래 참고 사항참조를 참조하십시오. |
|`transform`|(선택 사항) [지원되는](#mapping-transformations)변환이 있는 콘텐츠에 적용해야 하는 변환입니다.|

**참고 사항**
>[!NOTE]
> * `field`함께 `path` 사용할 수 없으며 하나만 허용됩니다. 
> * `path`루트만 `$` 을 가리킬 수 없으며 하나 이상의 경로 수준이 있어야 합니다.

아래 두 가지 대안은 동일합니다.

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
> 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**참고:** `Properties` 속성 가방이 없는 다음 매핑 형식은 현재 지원되지만 나중에 더 이상 사용되지 않을 수 있습니다.

```
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

## <a name="parquet-mapping"></a>마루 매핑

소스 파일이 Parquet 형식이면 파일 내용이 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식을 지정하지 않는 한 테이블은 Kusto 데이터베이스에 있어야 합니다. Parquet 매핑에 매핑된 열은 모든 비존재하지 않는 열에 대해 데이터 형식을 지정하지 않는 한 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대한 매핑을 설명하며 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`path`|`$`로 시작하는 경우 : 마루 문서의 열의 내용이 될 필드에 JSON 경로 (전체 문서를 나타내는 `$`JSON 경로입니다). 값이 시작되지 않으면 `$`: 상수 값이 사용됩니다.|
|`transform`|(선택 사항) 콘텐츠에 적용해야 하는 [매핑 변환을](#mapping-transformations) 제공합니다.


### <a name="example-of-the-parquet-mapping"></a>마루 매핑의 예

``` json
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
> 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

* 위의 매핑을 [미리 만든](create-ingestion-mapping-command.md) 경우 `.ingest` 제어 명령에서 참조할 수 있습니다.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

```
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

## <a name="orc-mapping"></a>오크 매핑

원본 파일이 Orc 형식이면 파일 콘텐츠가 Kusto 테이블에 매핑됩니다. 매핑된 모든 열에 대해 유효한 데이터 형식을 지정하지 않는 한 테이블은 Kusto 데이터베이스에 있어야 합니다. Orc 매핑에 매핑된 열은 모든 비존재하지 않는 열에 대해 데이터 형식을 지정하지 않는 한 Kusto 테이블에 있어야 합니다.

목록의 각 요소는 특정 열에 대한 매핑을 설명하며 다음 속성을 포함할 수 있습니다.

|속성|Description|
|----|--|
|`path`|`$`로 시작하는 경우 : Orc 문서의 열 의 내용이 될 필드에 대한 JSON 경로 (전체 `$`문서를 나타내는 JSON 경로는). 값이 시작되지 않으면 `$`: 상수 값이 사용됩니다.|
|`transform`|(선택 사항) 콘텐츠에 적용해야 하는 [매핑 변환을](#mapping-transformations) 제공합니다.

### <a name="example-of-orc-mapping"></a>오크 매핑의 예

``` json
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
> 위의 매핑이 `.ingest` 제어 명령의 일부로 제공되면 JSON 문자열로 직렬화됩니다.

```
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

## <a name="mapping-transformations"></a>매핑 변환

일부 데이터 형식 매핑(마루, JSON 및 Avro)은 간단하고 유용한 인제스트 타임 변환을 지원합니다. 시나리오에서 인제스트 타임에 더 복잡한 처리가 필요한 경우 KQL 식을 사용하여 경량 처리를 정의할 수 있는 [업데이트 정책을](update-policy.md)사용합니다.

|경로 종속 변환|Description|조건|
|--|--|--|
|`PropertyBagArrayToDictionary`|JSON 속성 배열(예: {events:[{"n1":"v1"},{"n2":"v2}}})을 사전으로 변환하고 유효한 JSON 문서(예: {"n1":"v1","n2":"v2"})로 직렬화합니다.|사용 시에만 `path` 적용 가능합니다.|
|`GetPathElement(index)`|주어진 인덱스에 따라 지정된 경로에서 요소를 추출합니다(예: $.a.b.c, GetPathElement(0) == "c", GetPathElement(-1) == "b", 형식 문자열|사용 시에만 `path` 적용 가능합니다.|
|`SourceLocation`|데이터 형식 문자열을 제공한 저장소 아티팩트의 이름(예: Blob의 "BaseUri" 필드).|
|`SourceLineNumber`|해당 저장소 아티팩트를 기준으로 오프셋하고 길게 입력합니다('1'로 시작하여 새 레코드당 증분).|
|`DateTimeFromUnixSeconds`|유닉스 타임(1970-01-01 이후초)을 나타내는 숫자를 UTC datetime 문자열로 변환합니다.|
|`DateTimeFromUnixMilliseconds`|유닉스 타임(1970-01-01 이후밀리초)을 나타내는 숫자를 UTC datetime 문자열로 변환합니다.|
|`DateTimeFromUnixMicroseconds`|유닉스 타임(1970-01-01 이후마이크로초)을 나타내는 숫자를 UTC datetime 문자열로 변환합니다.|
|`DateTimeFromUnixNanoseconds`|유닉스 타임(1970-01-01 이후의 나노초)을 나타내는 숫자를 UTC datetime 문자열로 변환합니다.|