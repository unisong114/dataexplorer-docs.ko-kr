---
title: 외부 데이터 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 외부 데이터 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515473"
---
# <a name="externaldata-operator"></a>externaldata 연산자

연산자는 `externaldata` 쿼리 자체에 스키마가 정의되고 외부 저장소 아티팩트(예: Azure Blob Storage의 Blob)에서 데이터를 읽는 테이블을 반환합니다.

> [!NOTE]
> 이 연산자는 파이프라인 입력이 없습니다.

**구문**

`externaldata``(` *열 이름* `:` 열`,` *유형* [...] `)` `]` `with` `(` `=` *Value1* `,` *Prop1* *StorageConnectionString* 스토리지연결문자열 [ Prop1 값1 [...] `[` `)`]

**인수**

* *열 이름*, *열 유형*: 테이블의 스키마를 정의합니다.
  구문은 [.create 테이블에서](../management/create-table-command.md)테이블을 정의할 때 사용되는 구문과 동일합니다.

* *StorageConnectionString*: [저장소 연결 문자열은](../api/connection-strings/storage.md) 반환할 데이터를 보유하는 저장소 아티팩트를 설명합니다.

* *Prop1*, *Value1*, ...: [수집 속성](../management/data-ingestion/index.md)아래에 나열된 대로 저장소에서 검색된 데이터를 해석하는 방법을 설명하는 추가 속성 .
    * 현재 지원되는 `format` `ignoreFirstRecord`속성: 및 .
    * 지원되는 데이터 형식: [모든 수집 데이터 형식(는](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) `csv`)(, `tsv` `avro`". `json` `parquet`

**반환**

연산자는 `externaldata` 저장소 연결 문자열로 표시된 지정된 저장소 아티팩트에서 데이터가 구문 분석된 지정된 스키마의 데이터 테이블을 반환합니다.

**예제**

다음 예제에서는 `UserID` 열이 외부 Blob에서 보류된 알려진 아이디 집합(줄당 하나)에 속하는 테이블의 모든 레코드를 찾는 방법을 보여 주습니다.
집합은 쿼리에서 간접적으로 참조되므로 매우 클 수 있습니다.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```