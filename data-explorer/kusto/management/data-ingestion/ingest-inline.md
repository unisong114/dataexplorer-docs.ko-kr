---
title: . 수집 인라인 명령 (push)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 수집 인라인 명령 (push)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2b1766b295fd348639d8d91c8308a3ed0a35a3dc
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373405"
---
# <a name="the-ingest-inline-command-push"></a>. 수집 인라인 명령 (push)

이 명령은 명령 텍스트 자체에 인라인으로 포함 된 데이터를 "푸시" 하 여 테이블에 데이터를 수집 합니다.

> [!NOTE]
> 이 명령의 기본 목적은 수동 임시 테스트 목적입니다.
> 프로덕션 사용을 위해 [저장소에서 수집](./ingest-from-storage.md)하는 것과 같이 대량의 데이터를 대량으로 전달 하는 데 더 적합 한 다른 수집 방법을 사용 하는 것이 좋습니다.

**구문**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|` *데이터*



**인수**

* *TableName* 은 데이터를 수집 하는 테이블의 이름입니다.
  테이블 이름은 항상 컨텍스트의 데이터베이스에 상대적 이며 스키마 매핑 개체가 제공 되지 않은 경우 해당 스키마는 데이터에 대해 가정 하는 스키마입니다.

* *데이터* 는 수집할 데이터 콘텐츠입니다. 수집 속성에 의해 수정 되지 않은 경우이 콘텐츠는 CSV로 구문 분석 됩니다.
  대부분의 제어 명령과 쿼리와 달리 명령의 *데이터* 부분 텍스트는 언어의 구문 규칙을 따르지 않아도 됩니다. 예를 들어 공백 문자는 중요 하 고 `//` 조합은 주석으로 처리 되지 않습니다.

* *IngestionPropertyName*, *IngestionPropertyValue*: 수집 프로세스에 영향을 주는 수집 [속성](../../../ingestion-properties.md) 은 수에 제한이 없습니다.

**결과**

명령의 결과는 명령에 의해 생성 된 데이터 분할 ("익스텐트") 만큼의 레코드를 포함 하는 테이블입니다.
데이터 분할 생성 되지 않은 경우에는 빈 (0 값) 익스텐트 ID를 사용 하 여 단일 레코드가 반환 됩니다.

|속성       |Type      |설명                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |명령으로 생성 된 데이터 분할에 대 한 고유 식별자입니다.|

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
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->