---
title: LightIngest를 사용 하 여 데이터를 Azure 데이터 탐색기에 수집 합니다.
description: Azure 데이터 탐색기에 대 한 임시 데이터 수집에 대 한 명령줄 유틸리티인 LightIngest에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/28/2020
ms.openlocfilehash: a56f5ea3ad17e8ef7c428d927861af9eaa0e9935
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264757"
---
# <a name="use-lightingest-to-ingest-data-to-azure-data-explorer"></a>LightIngest를 사용 하 여 Azure 데이터 탐색기에 데이터 수집
 
LightIngest는 Azure 데이터 탐색기에 대 한 임시 데이터 수집을 위한 명령줄 유틸리티입니다. 유틸리티는 로컬 폴더 또는 Azure blob 저장소 컨테이너에서 원본 데이터를 끌어올 수 있습니다.
LightIngest는 수집 기간에 시간 제한이 없기 때문에 많은 양의 데이터를 수집 하려는 경우에 가장 유용 합니다. 이는 나중에 생성 된 시간에 따라 레코드를 쿼리 하 고 수집 시간이 아닌 경우에도 유용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* LightIngest- [Microsoft Azure .Kusto. Tools NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 의 일부로 다운로드 합니다.

    :::image type="content" source="media/lightingest/lightingest-download-area.png" alt-text="Lightingest 다운로드":::

* WinRAR- [www.win-rar.com/download.html](http://www.win-rar.com/download.html) 에서 다운로드 합니다.

## <a name="install-lightingest"></a>LightIngest 설치

1. LightIngest를 다운로드 한 컴퓨터의 위치로 이동 합니다.
1. WinRAR를 사용 하 여 *도구* 디렉터리를 컴퓨터에 추출 합니다.

## <a name="run-lightingest"></a>LightIngest 실행

1. 컴퓨터에서 추출 된 *tools* 디렉터리로 이동 합니다.
1. 위치 표시줄에서 기존 위치 정보를 삭제 합니다.
    
    :::image type="content" source="kusto/tools/images/KustoTools-Lightingest/lightingest-locationbar.png" alt-text="LightIngest에 대 한 기존 위치 정보 삭제":::

1. `cmd`을 입력하고 **Enter** 키를 누릅니다.
1. 명령 프롬프트에서를 입력 한 `LightIngest.exe` 다음 관련 명령줄 인수를 입력 합니다.

    > [!Tip]
    > 지원 되는 명령줄 인수 목록을 보려면를 입력 하십시오 `LightIngest.exe /help` .
    >
    > :::image type="content" source="media/lightingest/lightingest-cmd-line-help.png" alt-text="LightIngest에 대 한 명령줄 도움말":::

1. `ingest-`그런 다음 수집을 관리할 Azure 데이터 탐색기 클러스터에 대 한 연결 문자열을 입력 합니다.
    연결 문자열을 큰따옴표로 묶고 [Kusto 연결 문자열 사양을](kusto/api/connection-strings/kusto.md)따릅니다.

    예를 들면 다음과 같습니다.

    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="recommendations"></a>권장 사항

* LightIngest가에서 수집 끝점을 사용 하는 것이 좋습니다 `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` . 이러한 방식으로 Azure 데이터 탐색기 서비스는 수집 부하를 관리할 수 있으며 일시적인 오류를 쉽게 복구할 수 있습니다. 그러나 엔진 엔드포인트 ()로 직접 작업 하도록 LightIngest를 구성할 수도 있습니다 `https://{yourClusterNameAndRegion}.kusto.windows.net` .

   > [!NOTE]
   > 엔진 끝점을 사용 하 여 직접 수집 하는 경우에는를 포함할 필요가 없습니다 `ingest-` . 그러나 엔진을 보호 하 고 수집 성공률을 높이기 위한 DM 기능은 없습니다.

* 최적의 수집 성능을 위해 LightIngest는 로컬 파일의 압축 되지 않은 크기를 예상할 수 있도록 원시 데이터 크기가 필요 합니다. 그러나 LightIngest는 먼저 파일을 다운로드 하지 않고 압축 된 blob의 원시 크기를 정확 하 게 추정 하지 못할 수 있습니다. 따라서 압축 된 blob을 수집 때 `rawSizeBytes` blob 메타 데이터의 속성을 압축 되지 않은 데이터 크기 (바이트)로 설정 합니다.

## <a name="command-line-arguments"></a>명령줄 인수

|인수 이름            |Type     |설명       |필수/선택 사항
|------------------------------|--------|----------|-----------------------------|
|                               |문자열   |수집을 처리 하는 Kusto 끝점을 지정 하는 [Azure 데이터 탐색기 연결 문자열](kusto/api/connection-strings/kusto.md) 입니다. 큰따옴표로 묶어야 합니다. | 필수
|-database,-db          |문자열  |대상 Azure 데이터 탐색기 데이터베이스 이름 | 선택 사항  |
|-테이블                  |문자열  |대상 Azure 데이터 탐색기 테이블 이름 | 필수 |
|-sourcePath,-source      |문자열  |Blob 컨테이너의 원본 파일 또는 루트 URI에 대 한 경로입니다. 데이터가 blob에 있는 경우에는 저장소 계정 키 또는 SAS를 포함 해야 합니다. 큰따옴표로 묶는 것이 좋습니다. |필수 |
|-접두사                  |문자열  |수집할 원본 데이터가 blob 저장소에 있는 경우이 URL 접두사는 컨테이너 이름을 제외 하 고 모든 blob에서 공유 됩니다. <br>예를 들어 데이터가에 있으면 `MyContainer/Dir1/Dir2` 접두사는 여야 합니다 `Dir1/Dir2` . 큰따옴표로 묶기를 권장 합니다. | 선택 사항  |
|-패턴        |문자열  |원본 파일/b s i d를 선택 하는 패턴입니다. 와일드 카드를 지원 합니다. 예들 들어 `"*.csv"`입니다. 큰따옴표로 묶는 것이 좋습니다. | 선택 사항  |
|-zipPattern     |문자열  |수집할 ZIP 보관 파일에서 파일을 선택할 때 사용할 정규식입니다.<br>보관 파일의 다른 모든 파일은 무시됩니다. 예들 들어 `"*.csv"`입니다. 큰따옴표로 묶는 것이 좋습니다. | 선택 사항  |
|-format,-f           |문자열  | 원본 데이터 형식입니다. [지원 되는 형식](ingestion-supported-formats.md) 중 하나 여야 합니다. | 선택 사항  |
|-ingestionMappingPath, -mappingPath |문자열  |수집 열 매핑의 로컬 파일 경로입니다. Json 및 Avro 형식의 필수 항목입니다. [데이터 매핑](kusto/management/mappings.md) 참조 | 선택 사항  |
|-ingestionMappingRef, -mappingRef  |문자열  |테이블에 대해 이전에 만든 수집 열 매핑의 이름입니다. Json 및 Avro 형식의 필수 항목입니다. [데이터 매핑](kusto/management/mappings.md) 참조 | 선택 사항  |
|-creationTimePattern      |문자열  |설정 되 면 파일 또는 blob 경로에서 CreationTime 속성을 추출 하는 데 사용 됩니다. [을 사용 하 여 `CreationTime` 데이터를 수집 하는 방법을](#how-to-ingest-data-using-creationtime) 참조 하세요. |선택 사항  |
|-ignoreFirstRow,-ignoreFirst |bool    |설정 하는 경우 각 파일/b i d의 첫 번째 레코드가 무시 됩니다 (예: 원본 데이터에 헤더가 있는 경우). | 선택 사항  |
|-태그            |문자열   |수집 데이터와 연결할 [태그](kusto/management/extents-overview.md#extent-tagging) 입니다. 여러 번 발생할 수 있습니다. | 선택 사항  |
|-dontWait           |bool     |' T r u e '로 설정 되 면 수집 완료를 기다리지 않습니다. 많은 양의 파일/b s 수집 경우 유용 합니다. |선택 사항  |
|-압축,-cr          |double |압축률 힌트입니다. Azure 데이터 탐색기 원시 데이터 크기를 평가 하는 데 도움이 되는 압축 된 파일/a p i를 수집 때 유용 합니다. 원래 크기를 압축 크기로 나눈 값 |선택 사항  |
|-limit,-l           |integer   |설정 하는 경우 수집을 처음 N 개 파일로 제한 합니다. |선택 사항  |
|-listOnly,-list        |bool    |설정 되 면 수집 하도록 선택 된 항목만 표시 됩니다.| 선택 사항  |
|-ingestTimeout   |integer  |모든 수집 작업 완료에 대 한 시간 제한 (분)입니다. 기본값은 `60`입니다.| 선택 사항  |
|-forceSync        |bool  |설정 되 면 동기 수집을 강제로 수행 합니다. 기본값은 `false`입니다. |선택 사항  |
|-dataBatchSize        |integer  |각 수집 작업의 총 크기 제한 (MB, 압축 되지 않음)을 설정 합니다. |선택 사항  |
|-filesInBatch            |integer |각 수집 작업의 파일/b a p 수 제한을 설정 합니다. |선택 사항  |
|-devTracing,-trace       |문자열    |설정 하는 경우에는 기본적으로 현재 디렉터리에 있는 로컬 디렉터리에 진단 로그가 기록 `RollingLogs` 되거나 스위치 값을 설정 하 여 수정할 수 있습니다. | 선택 사항  |

## <a name="azure-blob-specific-capabilities"></a>Azure blob 관련 기능

Azure blob과 함께 사용 하는 경우 LightIngest는 특정 blob 메타 데이터 속성을 사용 하 여 수집 프로세스를 보강 합니다.

|Metadata 속성                            | 사용                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 설정 하는 경우 압축 되지 않은 데이터 크기로 해석 됩니다.                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | UTC 타임 스탬프로 해석 됩니다. 설정 하는 경우 Kusto에서 만든 시간을 재정의 하는 데 사용 됩니다. 백필 시나리오에 유용 |

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

    ![Ingestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="how-to-ingest-data-using-creationtime"></a>CreationTime를 사용 하 여 데이터를 수집 하는 방법

기존 시스템에서 Azure 데이터 탐색기로 기록 데이터를 로드 하는 경우 모든 레코드가 수집 날짜와 동일 하 게 수신 됩니다. 수집 시간이 아니라 생성 시간을 기준으로 데이터를 분할할 수 있도록 하기 위해 인수를 사용할 수 있습니다 `-creationTimePattern` . `-creationTimePattern`인수는 `CreationTime` 파일 또는 blob 경로에서 속성을 추출 합니다. 패턴은 전체 항목 경로를 반영 하지 않아도 되며, 사용 하려는 타임 스탬프를 포함 하는 섹션에 불과합니다.

인수 값은 다음을 포함 해야 합니다.
* 작은따옴표 (접두사)로 묶인 타임 스탬프 형식 바로 앞에 있는 상수 텍스트
* 표준 [.Net DateTime 표기법](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) 의 타임 스탬프 형식
* 타임 스탬프 (접미사) 바로 다음에 오는 상수 텍스트입니다.

**예** 

* 다음과 같이 날짜/시간을 포함 하는 blob 이름. `historicalvalues19840101.parquet` (타임 스탬프는 연도에 대 한 4 자리, 월의 경우 두 자리, 월의 경우 두 자리)입니다. 
    
    인수 값은 `-creationTimePattern` 파일 이름 *"' historicalvalues'yyyyMMdd '. parquet '"* 의 일부입니다.

    ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 계층 구조 폴더 구조를 참조 하는 blob URI (예:) `https://storageaccount/container/folder/2002/12/01/blobname.extension` 

    인수 값은 `-creationTimePattern` 폴더 구조 *"' folder/' YYYY/MM/dd '/blob '"* 의 일부입니다.

   ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'folder/'yyyy/MM/dd'/blob'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>저장소 계정 키 또는 SAS 토큰을 사용 하 여 blob 수집

* 지정 된 저장소 계정 `ACCOUNT` , 폴더 `DIR` , 컨테이너 아래 `CONT` 및 패턴과 일치 하는 10 개의 blob을 수집 합니다.`*.csv.gz`
* Destination은 database `DB` , table 이며 `TABLE` 대상에서 수집 매핑이 `MAPPING` 사전 생성 됩니다.
* 이 도구는 수집 작업이 완료 될 때까지 대기 합니다.
* 대상 데이터베이스와 저장소 계정 키 및 SAS 토큰을 지정 하는 다양 한 옵션을 확인 합니다.

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

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>헤더 행을 제외 하 고 컨테이너의 모든 blob을 수집 합니다.

* 지정 된 저장소 계정 `ACCOUNT` , 폴더 `DIR1/DIR2` , 컨테이너 아래 `CONT` 및 패턴과 일치 하는 모든 blob을 수집 합니다.`*.csv.gz`
* Destination은 database `DB` , table 이며 `TABLE` 대상에서 수집 매핑이 `MAPPING` 사전 생성 됩니다.
* 원본 blob에는 헤더 줄이 포함 되어 있으므로 각 blob의 첫 번째 레코드를 삭제 하도록 도구에 지시 합니다.
* 이 도구는 수집을 위해 데이터를 게시 하 고 수집 작업이 완료 될 때까지 기다리지 않습니다.

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

### <a name="ingesting-all-json-files-from-a-path"></a>경로에서 모든 JSON 파일 수집

* 경로 아래에 있는 모든 파일 `PATH` 을 수집 합니다. 패턴과 일치 합니다.`*.json`
* Destination이 데이터베이스 `DB` , 테이블 `TABLE` 및 수집 매핑이 로컬 파일에 정의 되어 있습니다.`MAPPING_FILE_PATH`
* 이 도구는 수집을 위해 데이터를 게시 하 고 수집 작업이 완료 될 때까지 기다리지 않습니다.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>파일 수집 및 진단 추적 파일 쓰기

* 경로 아래에 있는 모든 파일 `PATH` 을 수집 합니다. 패턴과 일치 합니다.`*.json`
* Destination이 데이터베이스 `DB` , 테이블 `TABLE` 및 수집 매핑이 로컬 파일에 정의 되어 있습니다.`MAPPING_FILE_PATH`
* 이 도구는 수집을 위해 데이터를 게시 하 고 수집 작업이 완료 될 때까지 기다리지 않습니다.
* 진단 추적 파일은 폴더에 로컬로 기록 됩니다.`LOGS_PATH`

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
