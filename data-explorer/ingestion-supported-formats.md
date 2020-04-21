---
title: 수집을 위해 Azure 데이터 탐색기에서 지원하는 데이터 형식입니다.
description: 수집을 위해 Azure 데이터 탐색기에서 지원하는 다양한 데이터 및 압축 형식에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 530fc02c58b719f29ef43b118fad1ce8f207337b
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81502544"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>수집을 위해 Azure 데이터 탐색기에서 지원하는 데이터 형식

데이터 수집은 데이터가 테이블에 추가되고 Azure Data Explorer에서 쿼리에 사용할 수 있게 되는 프로세스입니다. 쿼리에서 수집을 제외한 모든 수집 메서드의 경우 데이터는 지원되는 형식 중 하나에 있어야 합니다. 다음 표는 Azure Data Explorer가 데이터 수집에 지원하는 형식을 나열하고 설명합니다.

|형식   |확장명   |Description|
|---------|------------|-----------|
|Avro     |`.avro`     |[Avro 컨테이너 파일](https://avro.apache.org/docs/current/)입니다. `null`, `deflate` 코드가 지원됩니다(`snappy`는 현재 지원되지 않음).|
|아파치아브로|`.avro`    |[논리적 형식](https://avro.apache.org/docs/current/spec.html#Logical+Types) 및 압축 코덱을 지원하는 `snappy` [Avro](https://avro.apache.org/docs/current/) 형식에 대한 실험적인 네이티브 구현입니다.|
|CSV      |`.csv`      |쉼표(`,`)로 구분된 값을 사용하는 텍스트 파일입니다. [RFC 4180: _CSV(쉼표 구분값) 파일에 대한 공통 형식 및 MIME 형식을_](https://www.ietf.org/rfc/rfc4180.txt)참조하십시오.|
|JSON     |`.json`     |JSON 개체가 `\n` 또는 `\r\n`으로 구분된 텍스트 파일입니다. [JSON Lines(JSONL)](http://jsonlines.org/)를 참조하세요.|
|멀티 제이슨|`.multijson`|JSON 속성 모음 배열(각각 레코드를 나타냄) 또는 공백으로 구분된 여러 속성 모음(`\n` 또는 `\r\n`)이 있는 텍스트 파일입니다. 각 속성 모음을 여러 줄에 분배할 수 있습니다. 데이터가 비속성 `JSON`봉투가 아닌 경우 를 통해 이 형식을 선호합니다.|
|ORC      |`.orc`      |[Orc 파일](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |[Parquet 파일](https://en.wikipedia.org/wiki/Apache_Parquet).|
|PSV      |`.psv`      |파이프(<code>&#124;</code>)로 구분된 값을 사용하는 텍스트 파일입니다.|
|RAW      |`.raw`      |전체 내용이 단일 문자열 값인 텍스트 파일입니다.|
|SCsv     |`.scsv`     |세미콜론(`;`)으로 구분된 값을 사용하는 텍스트 파일입니다.|
|소흐스프 ()소호프 ()소    |`.sohsv`    |SOH로 구분된 값을 사용하는 텍스트 파일입니다. (SOH는 ASCII 코드 포인트 1이며, 이 형식은 Hive on HDInsight에서 사용됩니다.)|
|TSV      |`.tsv`      |탭(`\t`)으로 구분된 값을 사용하는 텍스트 파일입니다.|
|TSVE     |`.tsv`      |탭(`\t`)으로 구분된 값을 사용하는 텍스트 파일입니다. 백슬래시(`\`) 문자가 이스케이프에 사용됩니다.|
|TXT      |`.txt`      |`\n`으로 구분된 줄을 사용하는 텍스트 파일입니다. 빈 줄은 건너뜁니다.|

## <a name="supported-data-compression-formats"></a>지원되는 데이터 압축 형식

Blob 및 파일은 다음 압축 알고리즘 중 일부를 통해 압축할 수 있습니다.

|압축|확장명|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Blob 또는 파일의 이름에 확장을 더하여 압축을 나타냅니다.

다음은 그 예입니다.
* `MyData.csv.zip`ZIP(아카이브 또는 단일 파일)으로 압축된 BLOB 또는 CSV로 포맷된 파일을 나타냅니다.
* `MyData.csv.gz`Blob 또는 CSV로 포맷된 파일을 나타내며 GZip으로 압축됩니다.

형식 확장자가 포함되지 않지만 압축(예: 압축)만 포함하는 Blob 또는 파일 이름도 지원됩니다. 이 경우 파일 형식을 유추할 수 없으므로 수집 속성으로 지정해야 합니다.

> [!NOTE]
> 일부 압축 형식은 압축된 스트림의 일부로 원래 파일 확장명을 추적합니다. 이 확장명은 일반적으로 파일 형식을 확인하는 경우에는 무시됩니다. 압축된 Blob 또는 파일의 이름에서 파일 형식을 확인할 수 없는 경우 `format` 수집 속성을 통해 이를 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 수집에](/azure/data-explorer/ingest-data-overview) 대해 자세히 알아보기
* [Azure 데이터 탐색기 데이터 수집 속성에](ingestion-properties.md) 대해 자세히 알아보기
