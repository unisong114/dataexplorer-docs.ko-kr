---
title: .ingest 인라인 명령(푸시) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .ingest 인라인 명령(푸시)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521372"
---
# <a name="the-ingest-inline-command-push"></a>.ingest 인라인 명령(푸시)

이 명령은 명령 텍스트 자체에 인라인으로 포함된 데이터를 "푸시"하여 데이터를 테이블로 수집합니다.

> [!NOTE]
> 이 명령의 주요 목적은 수동 임시 테스트 용입니다.
> 프로덕션 용으로는 [저장소에서](./ingest-from-storage.md)수집과 같은 방대한 양의 데이터를 대량으로 전달하는 데 더 적합한 다른 섭취 방법을 사용하는 것이 좋습니다.

**구문**

`.ingest``inline` `with` `(` `,` *TableName* `=` *IngestionPropertyValue* *IngestionPropertyName* 테이블 이름 [ 인제션속성 이름 인제스트속성값 [...] `into` `table` `)`] `<|` *데이터*



**인수**

* *TableName은* 데이터를 수집할 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스를 기준으로 하며 해당 스키마는 스키마 매핑 개체가 제공되지 않는 경우 데이터에 대해 가정되는 스키마입니다.

* *데이터는* 수집할 데이터 콘텐츠입니다. 인기 속성에 의해 달리 수정되지 않는 한 이 콘텐츠는 CSV로 구문 분석됩니다.
  대부분의 컨트롤 명령 및 쿼리와 달리 명령의 *Data* 부분의 텍스트는 언어의 구문 규칙을 따를 필요가 없습니다(예: 공백 문자가 `//` 중요하며 조합이 주석으로 처리되지 않음 등).

* *인스티온속성,* *인과속성값*: 인기 프로세스에 영향을 주는 모든 수의 [인기 속성입니다.](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)

**결과**

명령의 결과는 명령에 의해 생성된 데이터 샤드("익스텐츠")만큼의 레코드가 있는 테이블입니다.
데이터 샤드가 생성되지 않은 경우 빈(값 0) 익스텐트 ID를 사용하여 단일 레코드가 반환됩니다.

|속성       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|익스텐트 ID   |`guid`    |명령에 의해 생성된 데이터 샤드의 고유 식별자입니다.|

**예**

다음`Purchases`명령은 두 개의 `SKU` 열(형식) `string`및 (형식)이 `Quantity` `long`있는 테이블() 으로 데이터를 수집합니다.

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```



<!--
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->