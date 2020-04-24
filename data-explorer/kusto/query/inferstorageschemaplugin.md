---
title: infer_storage_schema 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 infer_storage_schema 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513909"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema 플러그인

이 플러그인은 외부 데이터의 스키마를 추론하고 [외부 테이블을 만들](../management/externaltables.md#create-or-alter-external-table)때 사용할 수 있는 CSL 스키마 문자열로 반환합니다.

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

**구문**

`evaluate` `infer_storage_schema(` *옵션* `)`

**인수**

단일 *Options* 인수는 요청의 `dynamic` 속성을 지정하는 속성 가방을 포함하는 형식의 상수 값입니다.

|이름                    |필수|Description|
|------------------------|--------|-----------|
|`StorageContainers`|예|저장된 데이터 [아티팩트에](../api/connection-strings/storage.md) 대한 접두사 URI를 나타내는 저장소 연결 문자열 목록|
|`DataFormat`|예|지원되는 [데이터 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)중 하나입니다.|
|`FileExtension`|예|이 파일 확장으로 끝나는 파일만 스캔합니다. 필수는 아니지만 지정하면 프로세스 속도가 빨라질 수 있습니다(또는 데이터 읽기 문제 제거).|
|`FileNamePrefix`|예|이 접두사로 시작하는 파일만 검색합니다. 필수는 아니지만 지정하면 프로세스 속도가 빨라질 수 있습니다.|
|`Mode`|예|스키마 추론 전략 중 하나: `any` `last`. `all`. 마지막으로 작성된 파일또는 모든 파일에서 데이터 스키마를 (처음 찾은) 파일에서 유추합니다. 기본값은 `last`입니다.|

**반환**

플러그인은 `infer_storage_schema` CSL 스키마 문자열을 포함하는 단일 행/열이 포함된 단일 결과 테이블을 반환합니다.

> [!NOTE]
> * 스키마 추론 전략 'all'은 발견된 *모든* 아티팩트를 읽고 스키마를 병합하는 것을 의미하기 때문에 매우 "비용이 많이 드는" 작업입니다.
> * 일부 반환 된 형식은 잘못된 형식 추측 (또는 스키마 병합 프로세스의 결과로)의 결과로 실제 형식이 아닐 수 있습니다. 따라서 외부 테이블을 만들기 전에 결과를 주의 깊게 검토하는 것이 좋습니다.

**예제**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*결과*

|CslSchema|
|---|
|app_id:문자열, user_id:long, event_time:datetime, 국가:문자열, 시티:문자열, device_type:문자열, device_vendor:문자열, ad_network:문자열:문자열: site_id:문자열, event_type:문자열, event_name:문자열, 유기적:문자열, days_from_install:int, 수익:real|