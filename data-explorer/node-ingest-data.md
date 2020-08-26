---
title: Azure Data Explorer Node 라이브러리를 사용하여 데이터 수집
description: 이 문서에서는 Node.js를 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: a579f3ac5eaa1c1029b68802a89ea0003f812bf6
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875262"
---
# <a name="ingest-data-using-the-azure-data-explorer-node-library"></a>Azure Data Explorer Node 라이브러리를 사용하여 데이터 수집

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기는 2개의 Node용 클라이언트 라이브러리, [수집 라이브러리](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) 및 [데이터 라이브러리](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)를 제공합니다. 이러한 라이브러리를 사용하여 데이터를 클러스터로 수집(로드)하고 코드에서 데이터를 쿼리할 수 있습니다. 이 문서에서는 먼저 테스트 클러스터에서 테이블 및 데이터 매핑을 만듭니다. 그런 다음, 클러스터 큐에 수집을 넣고 결과의 유효성을 검사합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독 외에도이 문서를 완료 하려면 다음이 필요 합니다.

* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

* 개발 컴퓨터에 설치된 [Node.js](https://nodejs.org/en/download/)

## <a name="install-the-data-and-ingest-libraries"></a>데이터 및 수집 라이브러리 설치

*azure-kusto-ingest* 및 *azure-kusto-data* 설치

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>import 문 및 상수 추가

라이브러리에서 클래스 가져오기

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
애플리케이션을 인증하기 위해 Azure 데이터 탐색기는 Azure Active Directory 테넌트 ID를 사용합니다. 테넌트 ID를 찾으려면 [Office 365 테넌트 ID 찾기](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)를 따르세요.

이 코드를 실행하기 전에 `authorityId`, `kustoUri`, `kustoIngestUri` 및 `kustoDatabase`의 값을 설정합니다.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

이제 연결 문자열을 구성합니다. 이 예제에서는 디바이스 인증을 사용하여 클러스터에 액세스합니다. 또한 Azure Active Directory 애플리케이션 인증서, 애플리케이션 키, 사용자와 암호를 사용할 수 있습니다.

이후 단계에서 대상 테이블 및 매핑을 만듭니다.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>소스 파일 정보 설정

추가 클래스를 가져오고 데이터 원본 파일에 대한 상수를 설정합니다. 이 예제에서는 Azure Blob Storage에 호스트된 예제 파일을 사용합니다. **Stormevents** 샘플 데이터 집합에는 [환경적 정보에 대 한 국가별 센터](https://www.ncdc.noaa.gov/stormevents/)의 날씨 관련 데이터가 포함 되어 있습니다.

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>테스트 클러스터에 테이블 만들기

`StormEvents.csv` 파일에 있는 데이터 스키마와 일치하는 테이블을 만듭니다. 이 코드가 실행되면 다음과 같은 메시지가 반환됩니다. *로그인하려면 웹 브라우저를 사용하여 https://microsoft.com/devicelogin 페이지를 열고 코드 XXXXXXXXX를 입력하여 인증하세요*. 단계에 따라 로그인한 후 돌아가서 다음 코드 블록을 실행합니다. 연결을 만드는 후속 코드 블록을 위해 다시 로그인해야 합니다.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>수집 매핑 정의

테이블을 만들 때 사용되는 데이터 형식과 열 이름에 들어오는 CSV 데이터를 매핑합니다.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>수집을 위해 메시지를 큐에 넣음

Blob Storage에서 데이터를 끌어온 후 Azure 데이터 탐색기에 수집하기 위해 메시지를 큐에 넣습니다.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>테이블에 데이터가 포함되어 있는지 검증

데이터가 테이블에 수집되었는지 검증합니다. 큐에 넣은 수집이 예약되고 데이터가 Azure 데이터 탐색기에 로드될 때까지 5~10분 정도 기다립니다. 그런 후, 다음 코드를 실행하여 `StormEvents` 테이블의 레코드 수를 가져옵니다.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>쿼리 문제 해결 실행

에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터에 연결 합니다. 데이터베이스에서 다음 명령을 실행하여 지난 4시간 동안 수집 실패가 있었는지 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

다음 명령을 실행하여 지난 4시간 동안 진행된 모든 수집 작업의 상태를 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>리소스 정리

다른 문서를 따르려면 만든 리소스를 유지 합니다. 그렇지 않으면 데이터베이스에서 다음 명령을 실행하여 `StormEvents` 테이블을 정리합니다.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>다음 단계

* [쿼리 작성](write-queries.md)
