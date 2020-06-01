---
title: 수집 인라인 명령 (push)-Azure 데이터 탐색기
description: 이 문서에서는 인라인 명령 수집 (push)을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2ac3a9a414d31492917cfb1768ce7bb1d7d8abb1
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257929"
---
# <a name="ingest-inline-command-push"></a>. 인라인 명령 수집 (push)

이 명령은 인라인으로 포함 된 데이터를 명령 텍스트 자체에 "푸시합니다" 하 여 데이터를 테이블에 수집 합니다.

> [!NOTE]
> 이 명령은 수동 임시 테스트에 사용 됩니다.
> 프로덕션 환경에서는 [저장소에서 수집](./ingest-from-storage.md)하는 것과 같이 대량의 데이터를 대량으로 전달 하는 데 더 적합 한 다른 수집 방법을 사용 하는 것이 좋습니다.

**구문**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|` *데이터*

**인수**

* *TableName* 은 데이터를 수집 하는 테이블의 이름입니다.
  이름은 항상 컨텍스트의 데이터베이스를 기준으로 합니다.
  스키마 매핑 개체가 제공 되지 않은 경우 테이블 스키마는 데이터에 대해 가정 하는 스키마입니다.

* *데이터* 는 수집할 데이터 콘텐츠입니다. 수집 속성에 의해 수정 되지 않은 경우이 콘텐츠는 CSV로 구문 분석 됩니다.
 
> [!NOTE]
> 대부분의 제어 명령과 쿼리와 달리 명령의 *데이터* 부분 텍스트는 언어의 구문 규칙을 따르지 않아도 됩니다. 예를 들어 공백 문자는 중요 하거나 `//` 조합이 주석으로 처리 되지 않습니다.

* *IngestionPropertyName*, *IngestionPropertyValue*: 수집 프로세스에 영향을 주는 수집 [속성](../../../ingestion-properties.md) 은 수에 제한이 없습니다.

**결과**

명령의 결과는 생성 된 데이터 분할 ("익스텐트") 만큼 많은 레코드를 포함 하는 테이블입니다.
데이터 분할 생성 되지 않으면 빈 (0 값) 익스텐트 ID를 사용 하 여 단일 레코드가 반환 됩니다.

|속성       |Type      |Description                                                 |
|-----------|----------|------------------------------------------------------------|
|ExtentId   |`guid`    |명령으로 생성 된 데이터 분할의 고유 식별자입니다.|

**예**

다음 명령을 사용 하 여 ( `Purchases` `SKU` 형식) `string` 및 `Quantity` (형식)의 두 열이 있는 테이블 ()에 데이터를 수집 `long` 합니다.

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```

<!--
You can generate inline ingests commands using the Kusto.Data client library. 
(Note that compression does let you embed new lines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->
