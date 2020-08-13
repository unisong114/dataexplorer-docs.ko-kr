---
title: 포함 파일
description: 포함 파일
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201732"
---
|**Property** | **속성 설명**|
|---|---|
| `rawSizeBytes` | 원시 (압축 되지 않은) 데이터의 크기입니다. Avro/ORC/Parquet의 경우 서식 지정 압축을 적용 하기 전의 크기입니다. 이 속성을 압축 되지 않은 데이터 크기 (바이트)로 설정 하 여 원래 데이터 크기를 제공 합니다.|
| `kustoTable` |  기존 대상 테이블의 이름입니다. `Table`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| `kustoDataFormat` |  데이터 형식입니다. `Data format`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| `kustoIngestionMappingReference` |  사용할 기존 수집 매핑의 이름입니다. `Column mapping`블레이드의 집합을 재정의 `Data Connection` 합니다.|
| `kustoIgnoreFirstRecord` | 로 설정 된 경우 `true` kusto는 blob의 첫 번째 행을 무시 합니다. 테이블 형식 데이터 (CSV, TSV 또는 이와 유사한)를 사용 하 여 헤더를 무시 합니다. |
| `kustoExtentTags` | 결과 범위에 첨부 될 [태그](../kusto/management/extents-overview.md#extent-tagging) 를 나타내는 문자열입니다. |
| `kustoCreationTime` |  ISO 8601 문자열로 형식이 지정 된 blob에 대 한 [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) 를 재정의 합니다. 백필에 사용 합니다. |
