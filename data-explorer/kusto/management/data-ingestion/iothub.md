---
title: IoT Hub에서 수집-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 IoT Hub 수집에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c3ed0fa8608f2be5739c1143a648230f792e5d40
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373399"
---
# <a name="ingest-from-iot-hub"></a>IoT Hub에서 수집

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) 는 클라우드에서 호스트 되는 관리 서비스로, IoT 응용 프로그램과이 응용 프로그램에서 관리 하는 장치 간의 양방향 통신용 중앙 메시지 허브 역할을 합니다. Azure 데이터 탐색기는 [Event Hub와 호환 되는 기본 제공 끝점](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)을 사용 하 여 고객 관리 IoT hub에서 지속적인 수집을 제공 합니다.

## <a name="data-format"></a>데이터 형식

* [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체 형식의 이벤트 허브 끝점에서 데이터를 읽습니다.
* 이벤트 페이로드는 [Azure 데이터 탐색기에서 지 원하는 형식](../../../ingestion-supported-formats.md)중 하나일 수 있습니다.

## <a name="ingestion-properties"></a>수집 속성

수집 속성은 수집 프로세스를 지시 합니다. 데이터를 라우팅하는 위치와 데이터를 처리 하는 방법을 설명 합니다. [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용 하 여 이벤트 수집의 수집 [속성](../../../ingestion-properties.md) 을 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |설명|
|---|---|
| 테이블 | 기존 대상 테이블의 이름 (대/소문자 구분)입니다. `Table`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| 형식 | 데이터 형식입니다. `Data format`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| IngestionMappingReference | 사용할 기존 수집 [매핑의](../create-ingestion-mapping-command.md) 이름입니다. `Column mapping`블레이드의 집합을 재정의 `Data Connection` 합니다.|
| Encoding |  데이터 인코딩입니다. 기본값은 UTF8입니다. 은 [.net에서 지원 되는 인코딩을](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)사용할 수 있습니다. |

## <a name="events-routing"></a>이벤트 라우팅

Azure 데이터 탐색기 클러스터에 대 한 IoT Hub 연결을 설정 하는 경우 대상 테이블 속성 (테이블 이름, 데이터 형식 및 매핑)을 지정 합니다. 이는 데이터에 대 한 기본 라우팅 이며, 라고도 `static routing` 합니다.
이벤트 속성을 사용 하 여 각 이벤트에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)에 지정 된 대로 데이터를 동적으로 라우팅하고이 이벤트에 대 한 정적 속성을 재정의 합니다.

## <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

시스템 속성은 이벤트가 수신 될 때 IoT Hub 서비스에 의해 설정 되는 속성을 저장 하는 데 사용 되는 컬렉션입니다. Azure 데이터 탐색기 IoT Hub 연결은 선택한 속성을 테이블의 데이터에 포함 시킵니다.

> [!Note]
> * `csv`매핑의 경우 속성은 레코드의 시작 부분에서 아래 표에 나열 된 순서 대로 추가 됩니다. 매핑의 경우 속성 `json` 은 다음 표의 속성 이름에 따라 추가 됩니다.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub는 다음과 같은 시스템 속성을 노출 합니다.

|속성 |설명|
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
| iothub-connection-auth-generation-id| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 장치의 connectionDeviceGenerationId (장치 id 속성 당)를 포함 합니다. |
| iothub-connection-auth-method| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 디바이스를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. |

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 속성을 포함 해야 합니다.

**테이블 스키마 예제**

데이터에 세 개의 열 ( `Timespan` , `Metric` 및 `Value` )이 포함 되 고 포함 된 속성이 및 인 경우 `x-opt-enqueued-time` `x-opt-offset` 이 명령을 사용 하 여 테이블 스키마를 만들거나 변경 합니다.

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

## <a name="create-iot-hub-connection"></a>IoT Hub 연결 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

아직 없는 경우 [Iot Hub를 만듭니다](../../../ingest-data-iot-hub.md#create-an-iot-hub).

> [!Note]
> * `device-to-cloud partitions`개수를 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해 야 합니다.
> * 소비자 그룹은 소비자 당 uniqe 되어야 *합니다* . Kusto 연결의 전용 소비자 그룹을 만듭니다. Azure Portal에서 리소스를 찾고로 이동 하 여 `Built-in endpoints` 새 소비자 그룹을 추가 합니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 데이터 수집 연결

* Azure Portal을 통해: [azure 데이터 탐색기 테이블을 IoT hub에 연결](../../../ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)합니다.
* Azure 데이터 탐색기 관리 .NET SDK 사용: [IoT Hub 데이터 연결 추가](../../../data-connection-iot-hub-csharp.md#add-an-iot-hub-data-connection)
* Azure 데이터 탐색기 management Python SDK 사용: [IoT Hub 데이터 연결 추가](../../../data-connection-iot-hub-python.md#add-an-iot-hub-data-connection)
* ARM 템플릿 사용: [Iot Hub 데이터 연결을 추가 하기 위한 Azure Resource Manager 템플릿](../../../data-connection-iot-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> **데이터에 라우팅** 정보가 선택 되어 있는 경우 이벤트 속성의 일부로 필요한 [라우팅](#events-routing) 정보를 제공 *해야* 합니다.

> [!Note]
> 연결이 설정 되 면 생성 시간이 지나면 큐에 넣은 이벤트에서 시작 하 여 데이터를 수집 합니다.

### <a name="generating-data"></a>데이터 생성

* 장치를 시뮬레이션 하 고 데이터를 생성 하는 [샘플 프로젝트](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) 를 참조 하세요.