---
title: externaldata 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 externaldata 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d46a8669c523955f74d3f489c7b10e5b0f7ccef6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373295"
---
# <a name="externaldata-operator"></a>externaldata 연산자

연산자는 해당 `externaldata` 스키마가 쿼리 자체에 정의 되어 있고 외부 저장소 아티팩트 (예: Azure Blob Storage의 blob)에서 데이터를 읽는 테이블을 반환 합니다.

> [!NOTE]
> 이 연산자에는 파이프라인 입력이 없습니다.

**구문**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

**인수**

* *ColumnName*, *ColumnType*: 테이블의 스키마를 정의 합니다.
  구문은 [create table](../management/create-table-command.md)에서 테이블을 정의할 때 사용 되는 구문과 같습니다.

* *StorageConnectionString*: [저장소 연결 문자열](../api/connection-strings/storage.md) 은 반환할 데이터를 보유 하는 저장소 아티팩트를 설명 합니다.

* *Prop1*, *Value1*, ...: 수집 [속성](../management/data-ingestion/index.md)에 나열 된 대로 저장소에서 검색 된 데이터를 해석 하는 방법을 설명 하는 추가 속성입니다.
    * 현재 지원 되는 속성은 `format` 및 `ignoreFirstRecord` 입니다.
    * 지원 되는 데이터 형식:, [ingestion data formats](../../ingestion-supported-formats.md) ,, `csv` `tsv` `json` `parquet` , `avro` 등의 수집 데이터 형식이 지원 됩니다.

**반환**

`externaldata`연산자는 저장소 연결 문자열이 나타내는 지정 된 저장소 아티팩트에서 데이터를 구문 분석 한 지정 된 스키마의 데이터 테이블을 반환 합니다.

**예제**

다음 예에서는 `UserID` 열이 외부 blob에서 한 줄에 하나씩 있는 알려진 id 집합에 속하는 테이블의 모든 레코드를 찾는 방법을 보여 줍니다.
이 집합은 쿼리에서 간접적으로 참조 되므로 매우 클 수 있습니다.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```