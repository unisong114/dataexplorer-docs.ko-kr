---
title: externaldata 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 외부 데이터 운영자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 698acc481a6313160602c939774a6029978df483
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348108"
---
# <a name="externaldata-operator"></a>externaldata 연산자

연산자는 해당 `externaldata` 스키마가 쿼리 자체에 정의 되어 있고 외부 저장소 아티팩트 (예: Azure Blob Storage의 blob)에서 데이터를 읽는 테이블을 반환 합니다.

## <a name="syntax"></a>구문

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

## <a name="arguments"></a>인수

* *ColumnName*, *ColumnType*: 인수는 테이블의 스키마를 정의 합니다.
  구문은 [create table](../management/create-table-command.md)에서 테이블을 정의할 때 사용 되는 구문과 같습니다.

* *StorageConnectionString*: [저장소 연결 문자열](../api/connection-strings/storage.md) 은 반환할 데이터를 보유 하는 저장소 아티팩트를 설명 합니다.

* *Prop1*, *Value1*, ...: 수집 [속성](../../ingestion-properties.md)에 나열 된 대로 저장소에서 검색 된 데이터를 해석 하는 방법을 설명 하는 추가 속성입니다.
    * 현재 지원 되는 속성은 `format` 및 `ignoreFirstRecord` 입니다.
    * 지원 되는 데이터 형식:, [ingestion data formats](../../ingestion-supported-formats.md) ,, `CSV` `TSV` `JSON` `Parquet` , `Avro` 등의 수집 데이터 형식이 지원 됩니다.

> [!NOTE]
> 이 연산자에는 파이프라인 입력이 없습니다.

## <a name="returns"></a>반환

`externaldata`연산자는 저장소 연결 문자열에 표시 된 지정 된 저장소 아티팩트에서 데이터를 구문 분석 한 지정 된 스키마의 데이터 테이블을 반환 합니다.

## <a name="examples"></a>예

다음 예에서는 `UserID` 열이 외부 blob에서 한 줄에 하나씩 있는 알려진 id 집합에 속하는 테이블의 모든 레코드를 찾는 방법을 보여 줍니다.
이 집합은 쿼리에서 간접적으로 참조 되므로 클 수 있습니다.

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

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
> 연산자는 데이터 분할을 인식 하지 못합니다 `externaldata()` .
