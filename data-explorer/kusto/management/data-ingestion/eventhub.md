---
title: 이벤트 허브에서 수집 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 이벤트 허브에서 인제스트에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521508"
---
# <a name="ingest-from-event-hub"></a>이벤트 허브에서 인제스트

[Azure Event Hubs는](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. Azure 데이터 탐색기는 고객 관리 이벤트 허브에서 지속적인 수집을 제공합니다. 

## <a name="data-format"></a>데이터 형식

* 데이터는 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체의 형태로 이벤트 허브에서 읽습니다.
* Azure [Data Explorer에서 지원하는 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)중 하나로 수집할 레코드가 하나 이상 포함될 수 있습니다.
* 압축 알고리즘을 사용하여 `GZip` 데이터를 압축할 수 있습니다. `Compression` [인스티온 속성으로](#ingestion-properties)지정해야 합니다.

> [!Note]
> * 압축 된 형식 (아브로, 마루, ORC)에 대 한 데이터 압축 지원 되지 않습니다.
> * 사용자 지정 인코딩 및 임베디드 [시스템 속성은](#event-system-properties-mapping) 압축된 데이터에서 지원되지 않습니다.

## <a name="ingestion-properties"></a>수집 속성

인기 속성은 인기 프로세스를 지시합니다. 데이터를 라우팅할 위치와 데이터를 처리하는 방법 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용하여 이벤트 수집의 [인기 속성을](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |Description|
|---|---|
| 테이블 | 기존 대상 테이블의 이름(대/소문자 구분)입니다. 블레이드의 `Table` 집합을 `Data Connection` 재정의합니다. |
| 형식 | 데이터 형식입니다. 블레이드의 `Data format` 집합을 `Data Connection` 재정의합니다. |
| 인기매핑매핑참조 | 사용할 기존 [인베이션 매핑의 이름입니다.](../create-ingestion-mapping-command.md) 블레이드의 `Column mapping` 집합을 `Data Connection` 재정의합니다.|
| 압축 | 데이터 압축, `None` (기본값) 또는 `GZip` 압축.|
| Encoding |  데이터 인코딩, 기본값은 UTF8입니다. [.NET 지원 인코딩](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)중 어느 한 개일 수 있습니다. |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>이벤트 라우팅

Azure Data Explorer 클러스터에 대한 이벤트 허브 연결을 설정할 때 대상 테이블 속성(테이블 이름, 데이터 형식, 압축 및 매핑)을 지정합니다. 이 라우팅은 데이터에 대한 기본 라우팅이라고도 `static routing`합니다.
이벤트 속성을 사용하여 각 이벤트에 대한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData.Properties에](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)지정된 대로 데이터를 동적으로 라우팅하여 이 이벤트의 정적 속성을 재정의합니다.

다음 샘플에서는 이벤트 허브 세부 정보를 설정하고 날씨 `WeatherMetrics`메트릭 데이터를 테이블로 보냅니다.
데이터는 형식입니다. `json` `mapping1`테이블에 `WeatherMetrics`미리 정의됩니다.

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

시스템 속성은 이벤트가 큐에 들어있는 시간에 이벤트 Hubs 서비스에 의해 설정된 속성을 저장하는 데 사용되는 컬렉션입니다. Azure 데이터 탐색기 이벤트 허브 연결은 선택한 속성을 테이블의 데이터 방문에 포함합니다.

> [!Note]
> * 단일 레코드 이벤트에는 시스템 속성이 지원됩니다.
> * 압축된 데이터에서 시스템 속성은 지원되지 않습니다.
> * 매핑의 경우 `csv` 속성은 아래 표에 나열된 순서대로 레코드의 시작 부분에 추가됩니다. 매핑의 경우 `json` 다음 표의 속성 이름에 따라 속성이 추가됩니다.

### <a name="event-hub-expose-the-following-system-properties"></a>이벤트 허브는 다음 시스템 속성을 노출합니다.

|속성 |데이터 형식 |Description|
|---|---|---|
| x-opt-enqueued-time |Datetime | 이벤트가 큐에 대기된 UTC 시간입니다. |
| x-opt-sequence-number |long | 이벤트 허브의 파티션 스트림 내의 이벤트의 논리적 시퀀스 번호입니다.
| x-opt-offset |문자열 | 이벤트 허브 파티션 스트림을 기준으로 한 이벤트의 오프셋입니다. 오프셋 식별자는 이벤트 허브 스트림의 파티션 내에서 고유합니다. |
| x-옵트 퍼블리셔 |문자열 | 메시지가 게시자 끝점으로 전송된 경우 게시자 이름입니다. |
| x-opt-partition-key |문자열 |이벤트를 저장한 해당 파티션의 파티션 키입니다. |

테이블의 **데이터 원본** 섹션에서 이벤트 **시스템 속성을** 선택한 경우 테이블 스키마 및 매핑에 속성을 포함해야 합니다.

**테이블 스키마 예제**

데이터에 세 개의 열`Timespan`(, `Metric` `Value`및) 및 포함 `x-opt-enqueued-time` 하는 `x-opt-offset`속성이 포함 하는 경우 는 이 명령을 사용 하 여 테이블 스키마를 만들거나 변경 합니다.

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 매핑 예제**

다음 명령을 실행하여 레코드의 시작 부분에 데이터를 추가합니다. 참고 서수 값: 속성은 위의 표에 나열된 순서대로 레코드의 시작 부분에 추가됩니다. 이는 매핑된 시스템 속성에 따라 열 서수들이 변경되는 CSV 매핑에 중요합니다.

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
 
**JSON 매핑 예제**

데이터는 **데이터 연결** 블레이드 **이벤트 시스템 속성** 목록에 나타나는 시스템 속성 이름을 사용하여 추가됩니다. 다음 명령을 실행합니다.

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
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 리전의 모든 리소스를 만듭니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

아직 없는 경우 [이벤트 허브 만들기.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) 템플릿은 [이벤트 허브 만들기](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub) 가이드에서 찾을 수 있습니다.

> [!Note]
> * 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다.
> * 소비자 그룹은 소비자당 유니크여야 *합니다.* Azure 데이터 탐색기 연결 전용 소비자 그룹을 만듭니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기의 데이터 수집 연결

* Azure 포털을 통해: [이벤트 허브에 연결합니다.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)
* Azure 데이터 탐색기 관리 .NET SDK 사용: [이벤트 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Azure 데이터 탐색기 관리 파이썬 SDK 사용: [이벤트 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* ARM 템플릿 사용: [이벤트 허브 데이터 연결을 추가하기 위한 Azure 리소스 관리자 템플릿](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> 내 데이터에 선택한 **라우팅 정보가 포함된** 경우 이벤트 속성의 일부로 필요한 [라우팅](#events-routing) 정보를 제공해야 *합니다.*

> [!Note]
> 연결이 설정되면 생성 시간 이후에 큐에 들어있는 이벤트에서 시작되는 데이터를 수집합니다.

#### <a name="generating-data"></a>데이터 생성

* 데이터를 생성하고 이벤트 허브로 보내는 [샘플 앱을](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 참조하십시오.

이벤트에는 크기 제한까지 하나 이상의 레코드가 포함될 수 있습니다. 다음 샘플에서는 두 개의 이벤트를 보내고 각 이벤트에는 5개의 레코드가 추가됩니다.

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