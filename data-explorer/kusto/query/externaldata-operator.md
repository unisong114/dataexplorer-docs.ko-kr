---
title: externaldata 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 외부 데이터 운영자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 4b54b7e1c16bce07c5ae97b57f0ebdb6c96a7ad7
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320618"
---
# <a name="externaldata-operator"></a>externaldata 연산자

`externaldata`연산자는 스키마가 쿼리 자체에 정의 되어 있고 외부 저장소 아티팩트 (예: Azure Blob Storage의 blob 또는 Azure Data Lake Storage 파일)에서 데이터를 읽을 수 있는 테이블을 반환 합니다.

## <a name="syntax"></a>Syntax

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*StorageConnectionString* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

## <a name="arguments"></a>인수

* *ColumnName*, *ColumnType*: 인수는 테이블의 스키마를 정의 합니다.
  구문은에서 테이블을 정의할 때 사용 되는 구문과 같습니다 [`.create table`](../management/create-table-command.md) .

* *StorageConnectionString*: 반환할 데이터를 보유 하는 저장소 아티팩트를 설명 하는 [저장소 연결 문자열](../api/connection-strings/storage.md) 입니다.

* *PropertyName*, *PropertyValue*, ...: 수집 [속성](../../ingestion-properties.md)에 나열 된 대로 저장소에서 검색 된 데이터를 해석 하는 방법을 설명 하는 추가 속성입니다.

현재 지원 되는 속성은 다음과 같습니다.

| 속성         | 형식     | 설명       |
|------------------|----------|-------------------|
| `format`         | `string` | 데이터 형식입니다. 지정 하지 않으면 파일 확장명 (기본값)에서 데이터 형식을 검색 하려고 시도 `CSV` 합니다. 수집 [데이터 형식이](../../ingestion-supported-formats.md) 지원 됩니다. |
| `ignoreFirstRecord` | `bool` | True로 설정 하면 모든 파일의 첫 번째 레코드가 무시 됨을 나타냅니다. 이 속성은 헤더를 사용 하 여 CSV 파일을 쿼리할 때 유용 합니다. |
| `ingestionMapping` | `string` | 원본 파일의 데이터를 연산자 결과 집합의 실제 열에 매핑하는 방법을 나타내는 문자열 값입니다. [데이터 매핑](../management/mappings.md)을 참조하세요. |


> [!NOTE]
> * 이 연산자는 파이프라인 입력을 허용 하지 않습니다.
> * 표준 [쿼리 제한은](../concepts/querylimits.md) 외부 데이터 쿼리에도 적용 됩니다.

## <a name="returns"></a>반환

`externaldata`연산자는 저장소 연결 문자열에 표시 된 지정 된 저장소 아티팩트에서 데이터를 구문 분석 한 지정 된 스키마의 데이터 테이블을 반환 합니다.

## <a name="examples"></a>예제

**Azure Blob Storage에 저장 된 사용자 Id 목록 가져오기**

다음 예에서는 `UserID` 열이 외부 저장소 파일에 있는 알려진 id 집합 (한 줄에 하나씩)에 속하는 테이블의 모든 레코드를 찾는 방법을 보여 줍니다. 데이터 형식이 지정 되지 않았기 때문에 검색 된 데이터 형식은 `TXT` 입니다.

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**여러 데이터 파일 쿼리**

다음 예제에서는 외부 저장소에 저장 된 여러 데이터 파일을 쿼리 합니다.

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

위의 예는 [외부 테이블](schema-entities/externaltables.md)을 정의 하지 않고 여러 데이터 파일을 쿼리 하는 빠른 방법으로 간주할 수 있습니다.

> [!NOTE]
> 연산자는 데이터 분할을 인식 하지 못합니다 `externaldata` .

**계층적 데이터 형식 쿼리**

,, 또는와 같은 계층적 데이터 형식을 `JSON` 쿼리하려면 `Parquet` `Avro` `ORC` `ingestionMapping` 연산자 속성에를 지정 해야 합니다. 이 예제에는 다음과 같은 내용으로 Azure Blob Storage에 저장 된 JSON 파일이 있습니다.

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

연산자를 사용 하 여이 파일을 쿼리하려면 `externaldata` 데이터 매핑을 지정 해야 합니다. 매핑은 JSON 필드를 연산자 결과 집합 열에 매핑하는 방법을 결정 합니다.

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

`MultiJSON`이 형식은 단일 JSON 레코드가 여러 줄로 확장 되기 때문에 사용 됩니다.

매핑 구문에 대한 자세한 내용은 [데이터 매핑](../management/mappings.md)을 참조하세요.
