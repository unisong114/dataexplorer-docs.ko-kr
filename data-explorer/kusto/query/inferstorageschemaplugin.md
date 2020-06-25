---
title: infer_storage_schema 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 infer_storage_schema 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 5fd6221e4b877d066050f932a564f71d56d8c168
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332555"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema 플러그 인

이 플러그 인은 외부 데이터의 스키마를 유추 하 고 CSL 스키마 문자열로 반환 합니다. [외부 테이블을 만들](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)때 문자열을 사용할 수 있습니다.

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

단일 *옵션* 인수는 `dynamic` 요청의 속성을 지정 하는 속성 모음을 보유 하는 형식의 상수 값입니다.

|이름                    |필수|설명|
|------------------------|--------|-----------|
|`StorageContainers`|예|저장 된 데이터 아티팩트의 접두사 URI를 나타내는 [저장소 연결 문자열](../api/connection-strings/storage.md) 목록|
|`DataFormat`|예|지원 되는 [데이터 형식](../../ingestion-supported-formats.md)중 하나입니다.|
|`FileExtension`|아니요|이 파일 확장명으로 끝나는 파일만 검색 합니다. 필수는 아니지만이를 지정 하 여 프로세스를 가속화 하거나 데이터 읽기 문제를 제거할 수 있습니다.|
|`FileNamePrefix`|아니요|이 접두사로 시작 하는 파일만 검색 합니다. 필수는 아니지만이를 지정 하면 프로세스의 속도를 높일 수 있습니다.|
|`Mode`|아니요|스키마 유추 전략,,, 중 하나입니다. `any` `last` `all` 각각의 (첫 번째) 파일, 마지막으로 쓴 파일 또는 모든 파일에서 데이터 스키마를 유추 합니다. 기본값은 `last`입니다.|

**반환**

`infer_storage_schema`플러그 인은 CSL 스키마 문자열을 포함 하는 단일 행/열을 포함 하는 단일 결과 테이블을 반환 합니다.

> [!NOTE]
> * 저장소 컨테이너 URI 비밀 키에는 *읽기*외에 *목록* 에 대 한 사용 권한이 있어야 합니다.
> * 스키마 유추 전략 ' 모두 '는 검색 된 *모든* 아티팩트를 읽고 해당 스키마를 병합 하는 것을 암시 하므로 매우 "비용이 많이 드는" 작업입니다.
> * 반환 된 형식이 잘못 된 형식 추측 (또는 스키마 병합 프로세스의 결과로)의 결과로 실제 형식이 아닐 수 있습니다. 이 때문에 외부 테이블을 만들기 전에 결과를 신중 하 게 검토 해야 합니다.

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
|app_id: string, user_id: long, event_time: datetime, country: string, city: string, device_type: string, device_vendor: string, ad_network: string, 캠페인: string, site_id: string, event_type: string, event_name: string, 유기적: string, days_from_install: int, 수익: real|
