---
title: 이벤트 허브에서 수집-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 이벤트 허브 수집을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: fac9fd9f218948928e4f91d0d1aa056affcebd11
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617666"
---
# <a name="ingest-from-event-hub"></a>이벤트 허브에서 수집

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) 는 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. Azure 데이터 탐색기는 고객 관리 Event Hubs에서 지속적인 수집을 제공 합니다. 

## <a name="data-format"></a>데이터 형식

* [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체의 형태로 이벤트 허브에서 데이터를 읽습니다.
* 이벤트 페이로드는 [Azure 데이터 탐색기에서 지 원하는 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)중 하나로 수집 될 하나 이상의 레코드를 포함할 수 있습니다.
* 압축 알고리즘을 사용 하 `GZip` 여 데이터를 압축할 수 있습니다. 수집 `Compression` [속성](#ingestion-properties)으로 지정 해야 합니다.

> [!Note]
> * 압축 형식 (Avro, Parquet, ORC)에는 데이터 압축이 지원 되지 않습니다.
> * 사용자 지정 인코딩 및 포함 된 [시스템 속성](#event-system-properties-mapping) 은 압축 된 데이터에서 지원 되지 않습니다.

## <a name="ingestion-properties"></a>수집 속성

수집 속성은 수집 프로세스를 지시 합니다. 데이터를 라우팅하는 위치와 데이터를 처리 하는 방법을 설명 합니다. [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용 하 여 이벤트 수집의 수집 [속성](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 을 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |설명|
|---|---|
| 테이블 | 기존 대상 테이블의 이름 (대/소문자 구분)입니다. `Data Connection` 블레이드의 집합을 `Table` 재정의 합니다. |
| 형식 | 데이터 형식입니다. `Data Connection` 블레이드의 집합을 `Data format` 재정의 합니다. |
| IngestionMappingReference | 사용할 기존 수집 [매핑의](../create-ingestion-mapping-command.md) 이름입니다. `Data Connection` 블레이드의 집합을 `Column mapping` 재정의 합니다.|
| 압축 | 데이터 압축 `None` (기본값) 또는 `GZip` 압축|
| Encoding |  데이터 인코딩입니다. 기본값은 UTF8입니다. 은 [.net에서 지원 되는 인코딩을](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)사용할 수 있습니다. |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>이벤트 라우팅

Azure 데이터 탐색기 클러스터에 대 한 이벤트 허브 연결을 설정할 때 대상 테이블 속성 (테이블 이름, 데이터 형식, 압축 및 매핑)을 지정 합니다. 이는 데이터에 대 한 기본 라우팅 이며, 라고도 `static routing`합니다.
이벤트 속성을 사용 하 여 각 이벤트에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)에 지정 된 대로 데이터를 동적으로 라우팅하고이 이벤트에 대 한 정적 속성을 재정의 합니다.

다음 샘플에서는 이벤트 허브 세부 정보를 설정 하 고 날씨 메트릭 데이터를 테이블 `WeatherMetrics`에 보냅니다.
데이터의 `json` 형식입니다. `mapping1`는 테이블 `WeatherMetrics`에 미리 정의 되어 있습니다.

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

시스템 속성은 이벤트를 큐에 넣을 때 Event Hubs 서비스에서 설정 하는 속성을 저장 하는 데 사용 되는 컬렉션입니다. Azure 데이터 탐색기 이벤트 허브 연결에서는 선택한 속성을 테이블의 데이터에 포함 합니다.

> [!Note]
> * 시스템 속성은 단일 레코드 이벤트에 대해 지원 됩니다.
> * 압축 된 데이터에서는 시스템 속성이 지원 되지 않습니다.
> * `csv` 매핑의 경우 속성은 레코드의 시작 부분에서 아래 표에 나열 된 순서 대로 추가 됩니다. `json` 매핑의 경우 속성은 다음 표의 속성 이름에 따라 추가 됩니다.

### <a name="event-hub-expose-the-following-system-properties"></a>이벤트 허브는 다음 시스템 속성을 노출 합니다.

|속성 |데이터 형식 |설명|
|---|---|---|
| x-opt-enqueued-time |Datetime | 이벤트를 큐에 넣은 UTC 시간입니다. |
| x-opt-sequence-number |long | 이벤트 허브의 파티션 스트림 내에 있는 이벤트의 논리적 시퀀스 번호입니다.
| x-opt-offset |string | 이벤트 허브 파티션 스트림에 상대적인 이벤트의 오프셋입니다. 오프셋 식별자는 이벤트 허브 스트림의 파티션 내에서 고유 합니다. |
| x opt-게시자 |string | 메시지를 게시자 끝점으로 보낸 경우 게시자 이름입니다. |
| x-opt-partition-key |string |이벤트를 저장 한 해당 파티션의 파티션 키입니다. |

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 속성을 포함 해야 합니다.

**테이블 스키마 예제**

데이터에 세 개의`Timespan`열 (, `Metric`및 `Value`)이 포함 되 `x-opt-enqueued-time` 고 포함 된 속성이 및 `x-opt-offset`인 경우이 명령을 사용 하 여 테이블 스키마를 만들거나 변경 합니다.

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 매핑 예**

다음 명령을 실행 하 여 레코드의 시작 부분에 데이터를 추가 합니다. 참고 서 수 값: 속성은 위의 표에 나열 된 순서 대로 레코드의 시작 부분에 추가 됩니다. 이는 매핑되는 시스템 속성에 따라 열 서 수가 변경 되는 CSV 매핑에 중요 합니다.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON 매핑 예**

데이터는 **데이터 연결** 블레이드 **이벤트 시스템 속성** 목록에 표시 되는 시스템 속성 이름을 사용 하 여 추가 됩니다. 다음 명령을 실행하세요.

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-event-hub-connection"></a>Event Hub 연결 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

아직 없는 경우 [이벤트 허브를 만듭니다](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). 템플릿은 [이벤트 허브를 만드는](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub) 방법 가이드에서 찾을 수 있습니다.

> [!Note]
> * 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다.
> * 소비자 그룹은 소비자 당 uniqe 되어야 *합니다* . Azure 데이터 탐색기 연결 전용 소비자 그룹을 만듭니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 데이터 수집 연결

* Azure Portal을 통해: [이벤트 허브에 연결](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)합니다.
* Azure 데이터 탐색기 관리 .NET SDK 사용: [이벤트 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Azure 데이터 탐색기 management Python SDK 사용: [이벤트 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* ARM 템플릿 사용: [이벤트 허브 데이터 연결을 추가 하기 위한 Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> **데이터에 라우팅** 정보가 선택 되어 있는 경우 이벤트 속성의 일부로 필요한 [라우팅](#events-routing) 정보를 제공 *해야* 합니다.

> [!Note]
> 연결이 설정 되 면 생성 시간이 지나면 큐에 넣은 이벤트에서 시작 하 여 데이터를 수집 합니다.

#### <a name="generating-data"></a>데이터 생성

* 데이터를 생성 하 고 이벤트 허브로 전송 하는 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 을 참조 하세요.

이벤트는 최대 크기 제한까지 하나 이상의 레코드를 포함할 수 있습니다. 다음 샘플에서는 두 개의 이벤트를 전송 합니다. 여기에는 각각 5 개의 레코드가 추가 됩니다.

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```
