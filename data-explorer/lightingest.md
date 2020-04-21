---
title: LightIngestAzure Azure 데이터 탐색기를 수집하기 위한 명령줄 유틸리티입니다.
description: Azure 데이터 탐색기로 의한 임시 데이터 수집을 위한 명령줄 유틸리티인 LightIngest에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e9918452aaeefc2c4f144c60e2e7bdb70ac87f2b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502414"
---
# <a name="install-and-use-lightingest"></a>LightIngest 설치 및 사용

LightIngestAzure Azure 데이터 탐색기로 임시 데이터 수집을 위한 명령줄 유틸리티입니다.
이 유틸리티는 로컬 폴더 또는 Azure Blob 저장소 컨테이너에서 원본 데이터를 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* LightIngest - [Microsoft.Azure.Kusto.Tools NuGet 패키지의](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 일부로 다운로드

    ![조명 다운로드](media/lightingest/lightingest-download-area.png)

* WinRAR - [www.win-rar.com/download.html](http://www.win-rar.com/download.html) 다운로드

## <a name="install-lightingest"></a>조명 제거 설치

1. LightIngest를 다운로드한 컴퓨터의 위치로 이동합니다. 
1. WinRAR을 사용하여 컴퓨터에 *도구* 디렉토리를 추출합니다.

## <a name="run-lightingest"></a>조명 을 실행

1. 컴퓨터에서 추출된 *도구* 디렉토리로 이동합니다.
1. 위치 표시줄에서 기존 위치 정보를 삭제합니다.
    
      ![위치 정보 삭제](media/lightingest/lightingest-location-bar.png)

1. Enter `cmd` 를 입력하고 **누릅니다.**
1. 명령 프롬프트에서 `LightIngest.exe` 관련 명령줄 인수 다음에 입력합니다.

    > [!Tip]
    > 지원되는 명령줄 인수 목록의 경우 `LightIngest.exe /help`을 입력합니다.
    >
    >![명령줄 도움말](media/lightingest/lightingest-cmd-line-help.png)

1. 수집을 관리하는 Azure Data Explorer 클러스터에 연결 문자열다음에 입력합니다. `ingest-`
    연결 문자열을 큰따옴표로 묶고 [Kusto 연결 문자열 사양을](kusto/api/connection-strings/kusto.md)따릅니다.

    다음은 그 예입니다.
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 가장 좋은 방법은 LightIngest에서 `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`섭취 끝점으로 작업하는 것입니다. 이렇게 하면 Azure Data Explorer 서비스가 수집 로드를 관리할 수 있으며 일시적인 오류로부터 쉽게 복구할 수 있습니다. 그러나 엔진 끝점()에서`https://{yourClusterNameAndRegion}.kusto.windows.net`직접 작동하도록 LightIngest를 구성할 수도 있습니다.

> [!Note]
> 엔진 끝점을 직접 섭취하는 경우 포함할 `ingest-`필요는 없지만 엔진을 보호하고 섭취 성공률을 높이는 DM 기능은 없습니다.

* 최적의 수집 성능을 위해 LightIngest는 원시 데이터 크기를 알고 있으므로 LightIngest는 압축되지 않은 로컬 파일 크기를 추정하는 것이 중요합니다. 그러나 LightIngest는 압축된 Blob의 원시 크기를 먼저 다운로드하지 않고는 정확하게 추정하지 못할 수 있습니다. 따라서 압축된 Blob을 수집할 때 `rawSizeBytes` Blob 메타데이터의 속성을 압축되지 않은 데이터 크기로 바이트로 설정합니다.

## <a name="general-command-line-arguments"></a>일반 명령줄 인수

|인수 이름         |짧은 이름   |Type    |필수 |Description                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |문자열  |필수 |수집을 처리할 Kusto 끝점을 지정하는 [Azure 데이터 탐색기 연결 문자열입니다.](kusto/api/connection-strings/kusto.md) 큰따옴표로 동봉되어야 합니다. |
|-데이터베이스             |-db          |문자열  |옵션  |대상 Azure 데이터 탐색기 데이터베이스 이름 |
|- 테이블                |             |문자열  |필수 |대상 Azure 데이터 탐색기 테이블 이름 |
|-sourcePath           |-source      |문자열  |필수 |Blob 컨테이너의 소스 파일 또는 루트 URI에 대한 경로입니다. 데이터가 Blob에 있는 경우 저장소 계정 키 또는 SAS를 포함해야 합니다. 큰따옴표로 동봉하는 것이 좋습니다. |
|-접두사               |             |문자열  |옵션  |수집할 원본 데이터가 Blob 저장소에 있는 경우 이 URL 접두사는 컨테이너 이름을 제외한 모든 Blob에서 공유됩니다. <br>예를 들어 데이터가 `MyContainer/Dir1/Dir2`에 있는 경우 접두사는 `Dir1/Dir2`이면 에 있어야 합니다. 큰따옴표로 둘러싸는 것이 좋습니다. |
|- 패턴              |             |문자열  |옵션  |원본 파일/Blob을 선택한 패턴입니다. 와일드카드를 지원합니다. 예: `"*.csv"`. 큰따옴표로 동봉하는 것이 좋습니다. |
|- 지퍼 패턴           |             |문자열  |옵션  |ZIP 아카이브에서 수집할 파일을 선택할 때 사용할 정규식입니다.<br>아카이브의 다른 모든 파일은 무시됩니다. 예를 들어, `"*.csv"`. 그것은 큰 따옴표로 둘러싸는 것이 좋습니다 |
|-형식               |-f           |문자열  |옵션  |원본 데이터 형식입니다. [지원되는 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) 중 하나여야 합니다. |
|-섭취매핑경로 |-매핑 경로 |문자열  |옵션  |열 매핑 파일(Json 및 Avro 형식의 경우 필수)을 들어오려는 경로입니다. [데이터 매핑](kusto/management/mappings.md) 보기 |
|-섭취매핑 레프  |-매핑참조  |문자열  |옵션  |미리 만들어진 작성 열 매핑의 이름입니다(Json 및 Avro 형식의 경우 필수). [데이터 매핑](kusto/management/mappings.md) 보기 |
|- 생성시간 패턴  |             |문자열  |옵션  |설정하면 파일 또는 Blob 경로에서 CreationTime 속성을 추출하는 데 사용됩니다. [생성시간 패턴 인수 사용](#using-creationtimepattern-argument) 참조 |
|-무시첫 번째 로       |-무시먼저 |bool    |옵션  |설정하면 각 파일/Blob의 첫 번째 레코드가 무시됩니다(예: 원본 데이터에 헤더가 있는 경우). |
|-태그                  |             |문자열  |옵션  |수집된 데이터와 연결할 [태그입니다.](kusto/management/extents-overview.md#extent-tagging) 여러 번의 발생이 허용됩니다. |
|-dontWait             |             |bool    |옵션  |'true'로 설정된 경우 완료를 기다리지 않습니다. 대량의 파일/Blob을 섭취할 때 유용합니다. |

### <a name="using-creationtimepattern-argument"></a>생성시간 패턴 인수 사용

인수는 `-creationTimePattern` 파일 또는 Blob 경로에서 CreationTime 속성을 추출합니다. 패턴은 전체 항목 경로를 반영할 필요가 없으며 사용하려는 타임스탬프를 둘러싸는 섹션만 반영할 수 있습니다.

인수 값에는 다음이 포함되어야 합니다.
* 타임스탬프 바로 앞에 있는 상수 테스트(단일 따옴표로 둘러싸인 경우)
* 표준 [.NET DateTime 표기형의](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) 타임스탬프 형식
* 타임스탬프 바로 다음에 있는 상수 텍스트입니다. 예를 들어 Blob 이름이 `historicalvalues19840101.parquet` 끝나는 경우(타임스탬프는 연도의 네 자리, 월의 두 자리, 해당 월의 두 자리)로 `-creationTimePattern` 끝나는 경우 인수의 해당 값은 다음과 같습니다.

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>고급 시나리오에 대한 명령줄 인수

|인수 이름         |짧은 이름   |Type    |필수 |Description                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|- 압축          |-cr          |double  |옵션  |압축 비율 힌트. Azure Data 탐색기가 원시 데이터 크기를 평가하는 데 도움이 되는 압축된 파일/Blob을 수집할 때 유용합니다. 원래 크기로 압축 크기로 나눈 값으로 계산 |
|- 제한                |-l           |integer |옵션  |설정하면 첫 번째 N 파일로 인기를 제한합니다. |
|-목록만             |-list        |bool    |옵션  |설정된 경우 인레이션을 위해 선택된 항목만 표시됩니다.| 
|-인제스트타임아웃        |             |integer |옵션  |모든 인제스트 작업 완료에 대한 시간 초과(분)입니다. 기본값은 `60`입니다.|
|-포스싱크            |             |bool    |옵션  |설정된 경우 동기 인칭을 강제로 변환합니다. 기본값은 `false`입니다. |
|-dataBatchSize        |             |integer |옵션  |각 인제스트 작업의 총 크기 제한(MB, 압축되지 않은) 설정 |
|-파일인배치         |             |integer |옵션  |각 인제스트 작업의 파일/Blob 개수 제한 설정 |
|-데브트레이싱           |- 추적       |문자열  |옵션  |설정하면 진단 로그가 로컬 디렉터리로 기록됩니다(기본적으로 현재 `RollingLogs` 디렉터리에서 또는 스위치 값을 설정하여 수정할 수 있음). |

## <a name="blob-metadata-properties"></a>Blob 메타데이터 속성
Azure Blob과 함께 사용할 경우 LightIngest는 특정 Blob 메타데이터 속성을 사용하여 수집 프로세스를 보강합니다.

|메타데이터 속성                            | 사용                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 설정된 경우 압축되지 않은 데이터 크기로 해석됩니다.                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | UTC 타임스탬프로 해석됩니다. 설정된 경우 Kusto의 생성 시간을 재정의하는 데 사용됩니다. 백필 시나리오에 유용 |

## <a name="usage-examples"></a>사용 예

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>저장소 계정 키 또는 SAS 토큰을 사용하여 Blob 을 가져옵니다.

* 지정된 저장소 계정, `ACCOUNT`폴더, `DIR`컨테이너 `CONT`아래, 패턴 일치 에서 10 Blob을 생성합니다.`*.csv.gz`
* 대상은 `DB`데이터베이스 `TABLE`, 테이블이며, 인기 매핑은 `MAPPING` 대상에서 미리 생성됩니다.
* 이 도구는 인제스트 작업이 완료될 때까지 기다립니다.
* 대상 데이터베이스 및 저장소 계정 키와 SAS 토큰을 지정하기 위한 다양한 옵션에 유의하십시오.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>헤더 행을 포함하지 않는 컨테이너의 모든 Blob 인집

* 지정된 저장소 계정, `ACCOUNT`폴더, `DIR1/DIR2`컨테이너 `CONT`아래의 모든 Blob을 인제하고 패턴과 일치합니다.`*.csv.gz`
* 대상은 `DB`데이터베이스 `TABLE`, 테이블이며, 인기 매핑은 `MAPPING` 대상에서 미리 생성됩니다.
* 소스 Blob에는 헤더 선이 포함되어 있으므로 도구는 각 Blob의 첫 번째 레코드를 삭제하라는 지시를 받습니다.
* 이 도구는 수집을 위한 데이터를 게시하고 수집 작업이 완료될 때까지 기다리지 않습니다.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>경로에서 모든 JSON 파일 인데스트

* 패턴과 일치하는 경로 `PATH`아래의 모든 파일 인더스트`*.json`
* 대상은 `DB`데이터베이스, `TABLE`테이블이며, 인기 매핑은 로컬 파일에 정의됩니다.`MAPPING_FILE_PATH`
* 이 도구는 수집을 위한 데이터를 게시하고 수집 작업이 완료될 때까지 기다리지 않습니다.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>파일 인과 진단 추적 파일 작성

* 패턴과 일치하는 경로 `PATH`아래의 모든 파일 인더스트`*.json`
* 대상은 `DB`데이터베이스, `TABLE`테이블이며, 인기 매핑은 로컬 파일에 정의됩니다.`MAPPING_FILE_PATH`
* 이 도구는 수집을 위한 데이터를 게시하고 수집 작업이 완료될 때까지 기다리지 않습니다.
* 진단 추적 파일은 폴더 아래에 로컬로 기록됩니다.`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Changelog
|버전        |변경                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>`-zipPattern` 인수가 에 추가됨</li><li>`-listOnly` 인수가 에 추가됨</li><li>실행이 시작되기 전에 인수 요약이 표시됩니다.</li><li>CreationTime 은 인수에 따라 Blob 메타데이터 속성 또는 `-creationTimePattern` Blob 또는 파일 이름에서 읽습니다.</li></ul>|
