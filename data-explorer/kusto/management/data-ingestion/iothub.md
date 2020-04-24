---
title: IoT Hub에서 수집 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 IoT Hub에서 수집에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521355"
---
# <a name="ingest-from-iot-hub"></a>IoT 허브에서 섭취하기

[Azure IoT Hub는](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) 클라우드에서 호스팅되는 관리형 서비스로, IoT 응용 프로그램과 관리하는 장치 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. Azure Data Explorer는 [엔드포인트에 내장된 이벤트 허브 호환을](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)사용하여 고객 관리 IoT Hubs에서 지속적인 수집을 제공합니다.

## <a name="data-format"></a>데이터 형식

* 데이터는 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체의 형태로 이벤트 허브 끝점에서 읽습니다.
* 이벤트 페이로드는 [Azure 데이터 탐색기에서 지원하는 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)중 하나에 있을 수 있습니다.

## <a name="ingestion-properties"></a>수집 속성

인기 속성은 인기 프로세스를 지시합니다. 데이터를 라우팅할 위치와 데이터를 처리하는 방법 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용하여 이벤트 수집의 [인기 속성을](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |Description|
|---|---|
| 테이블 | 기존 대상 테이블의 이름(대/소문자 구분)입니다. 블레이드의 `Table` 집합을 `Data Connection` 재정의합니다. |
| 형식 | 데이터 형식입니다. 블레이드의 `Data format` 집합을 `Data Connection` 재정의합니다. |
| 인기매핑매핑참조 | 사용할 기존 [인베이션 매핑의 이름입니다.](../create-ingestion-mapping-command.md) 블레이드의 `Column mapping` 집합을 `Data Connection` 재정의합니다.|
| Encoding |  데이터 인코딩, 기본값은 UTF8입니다. [.NET 지원 인코딩](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)중 어느 한 개일 수 있습니다. |

## <a name="events-routing"></a>이벤트 라우팅

Azure Data Explorer 클러스터에 대한 IoT Hub 연결을 설정할 때 대상 테이블 속성(테이블 이름, 데이터 형식 및 매핑)을 지정합니다. 이 라우팅은 데이터에 대한 기본 라우팅이라고도 `static routing`합니다.
이벤트 속성을 사용하여 각 이벤트에 대한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData.Properties에](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)지정된 대로 데이터를 동적으로 라우팅하여 이 이벤트의 정적 속성을 재정의합니다.

## <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

시스템 속성은 이벤트가 수신되는 시간에 IoT Hubs 서비스에 의해 설정된 속성을 저장하는 데 사용되는 컬렉션입니다. Azure 데이터 탐색기 IoT Hub 연결테이블에 데이터 방문에 선택한 속성을 포함 합니다.

> [!Note]
> * 매핑의 경우 `csv` 속성은 아래 표에 나열된 순서대로 레코드의 시작 부분에 추가됩니다. 매핑의 경우 `json` 다음 표의 속성 이름에 따라 속성이 추가됩니다.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub는 다음과 같은 시스템 속성을 노출합니다.

|속성 |Description|
|---|---|
| message-id | 사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. |
| sequence-number | 숫자(디바이스 큐 별로 고유함)는 IoT Hub에서 각 클라우드-디바이스 메시지에 할당됩니다. |
| to | 클라우드-디바이스 메시지에 지정된 대상입니다. |
| absolute-expiry-time | 메시지 만료 날짜 및 시간입니다. |
| iothub-enqueuedtime | IoT Hub에서 장치-클라우드 메시지를 받은 날짜 및 시간입니다. |
| correlation-id| 일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |
| user-id| 메시지의 원본을 지정하는 데 사용되는 ID입니다. |
| iothub-ack| 피드백 메시지 생성기입니다. |
| iothub-connection-device-id| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 deviceId를 포함합니다. |
| iothub-connection-auth-generation-id| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 여기에는 메시지를 보낸 장치의 장치 생성 Id(장치 ID 속성에 따라)가 포함되어 있습니다. |
| iothub-connection-auth-method| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 디바이스를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. |

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

## <a name="create-iot-hub-connection"></a>IoT 허브 연결 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 리전의 모든 리소스를 만듭니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

아직 없는 경우 [Iot Hub를 만듭니다.](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub)

> [!Note]
> * 개수는 `device-to-cloud partitions` 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다.
> * 소비자 그룹은 소비자당 유니크여야 *합니다.* Kusto 연결 전용 소비자 그룹을 만듭니다. Azure Portal에서 리소스를 찾고 `Built-in endpoints` 새 소비자 그룹을 추가합니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기의 데이터 수집 연결

* Azure 포털을 통해: [Azure 데이터 탐색기 테이블을 IoT 허브에 연결합니다.](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub)
* Azure 데이터 탐색기 관리 .NET SDK 사용: [IoT 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* Azure 데이터 탐색기 관리 파이썬 SDK 사용: [IoT 허브 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* ARM 템플릿 사용: [Iot Hub 데이터 연결을 추가하기 위한 Azure 리소스 관리자 템플릿](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> 내 데이터에 선택한 **라우팅 정보가 포함된** 경우 이벤트 속성의 일부로 필요한 [라우팅](#events-routing) 정보를 제공해야 *합니다.*

> [!Note]
> 연결이 설정되면 생성 시간 이후에 큐에 들어있는 이벤트에서 시작되는 데이터를 수집합니다.

### <a name="generating-data"></a>데이터 생성

* 장치를 시뮬레이션하고 데이터를 생성하는 [샘플 프로젝트를](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) 참조하십시오.