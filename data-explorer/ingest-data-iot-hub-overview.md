---
title: IoT Hub에서 수집-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 IoT Hub 수집에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 70e54259a6b7fa3fdeb2ef9843cc5d2df04229b9
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343184"
---
# <a name="create-a-connection-to-iot-hub"></a>IoT Hub에 대한 연결 만들기

[Azure IoT Hub](/azure/iot-hub/about-iot-hub) 는 클라우드에서 호스트 되는 관리 서비스로, IoT 응용 프로그램과이 응용 프로그램에서 관리 하는 장치 간의 양방향 통신용 중앙 메시지 허브 역할을 합니다. Azure 데이터 탐색기는 해당 [이벤트 허브와 호환 되는 기본 제공 끝점](/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)을 사용 하 여 고객이 관리 하는 IoT hub에서 지속적인 수집을 제공 합니다.

IoT 수집 파이프라인은 몇 가지 단계를 거칩니다. 먼저 IoT Hub를 만들고 장치를 등록 합니다. 그런 다음 지정 된 수집 [속성](#set-ingestion-properties)을 사용 하 여 [특정 형식의 데이터가](#data-format)수집 되는 Azure 데이터 탐색기에 대상 테이블을 만듭니다. Iot Hub 연결은 Azure 데이터 탐색기 테이블에 연결 하기 위한 [이벤트 라우팅을](#set-events-routing) 알고 있어야 합니다. [이벤트 시스템 속성 매핑에](#set-event-system-properties-mapping)따라 선택한 속성에 데이터를 포함 합니다. 이 프로세스는 [c #](data-connection-iot-hub-csharp.md) 또는 [Python](data-connection-iot-hub-python.md)을 사용 하 여 프로그래밍 방식으로 또는 [Azure Resource Manager 템플릿을](data-connection-iot-hub-resource-manager.md)사용 하 여 [Azure Portal](ingest-data-iot-hub.md)를 통해 관리할 수 있습니다.

Azure 데이터 탐색기에서 데이터를 수집 하는 방법에 대 한 일반적인 내용은 [azure 데이터 탐색기 데이터 수집 개요](ingest-data-overview.md)를 참조 하세요.

## <a name="data-format"></a>데이터 형식

* [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체 형식의 이벤트 허브 끝점에서 데이터를 읽습니다.
* [지원 되는 형식](ingestion-supported-formats.md)을 참조 하세요.
    > [!NOTE]
    > IoT Hub는 원시 형식을 지원 하지 않습니다.
* [지원 되는 압축](ingestion-supported-formats.md#supported-data-compression-formats)을 참조 하세요.
  * 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다. 그렇지 않으면 Azure 데이터 탐색기에서 예측 합니다. 파일당 압축 되지 않은 파일 크기 제한이 4gb입니다.

## <a name="set-ingestion-properties"></a>수집 속성 설정

수집 속성은 수집 프로세스에서 데이터를 라우팅하고 처리 하는 방법을 지시 합니다. [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용 하 여 이벤트의 수집 [속성](ingestion-properties.md) 을 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |설명|
|---|---|
| 테이블 | 기존 대상 테이블의 이름 (대/소문자 구분)입니다. `Table`창에서 집합을 재정의 합니다 `Data Connection` . |
| 형식 | 데이터 형식입니다. `Data format`창에서 집합을 재정의 합니다 `Data Connection` . |
| IngestionMappingReference | 사용할 기존 수집 [매핑의](kusto/management/create-ingestion-mapping-command.md) 이름입니다. `Column mapping`창에서 집합을 재정의 합니다 `Data Connection` .|
| Encoding |  데이터 인코딩입니다. 기본값은 UTF8입니다. 은 [.net에서 지원 되는 인코딩을](/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)사용할 수 있습니다. |

## <a name="set-events-routing"></a>이벤트 라우팅 설정

Azure 데이터 탐색기 클러스터에 대 한 IoT Hub 연결을 설정 하는 경우 대상 테이블 속성 (테이블 이름, 데이터 형식 및 매핑)을 지정 합니다. 이 설정은 데이터에 대 한 기본 라우팅으로, 정적 라우팅이 라고도 합니다.
이벤트 속성을 사용 하 여 각 이벤트에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)에 지정 된 대로 데이터를 동적으로 라우팅하고이 이벤트에 대 한 정적 속성을 재정의 합니다.

> [!Note]
> **데이터에 라우팅** 정보가 선택 되어 있는 경우 이벤트 속성의 일부로 필요한 라우팅 정보를 제공 해야 합니다.

## <a name="set-event-system-properties-mapping"></a>이벤트 시스템 속성 매핑 설정

시스템 속성은 이벤트를 받을 때 IoT Hub 서비스에서 설정한 속성을 저장 하는 데 사용 되는 컬렉션입니다. Azure 데이터 탐색기 IoT Hub 연결에는 테이블의 데이터 랜딩에 선택한 속성이 포함 됩니다.

> [!Note]
> `csv`매핑의 경우 속성은 레코드의 시작 부분에서 아래 표에 나열 된 순서 대로 추가 됩니다. 매핑의 경우 속성 `json` 은 다음 표의 속성 이름에 따라 추가 됩니다.

### <a name="system-properties"></a>시스템 속성

IoT Hub는 다음과 같은 시스템 속성을 노출 합니다.

|속성 |설명|
|---|---|
| message-id | 사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. |
| sequence-number | 숫자(디바이스 큐 별로 고유함)는 IoT Hub에서 각 클라우드-디바이스 메시지에 할당됩니다. |
| to | 클라우드-디바이스 메시지에 지정된 대상입니다. |
| absolute-expiry-time | 메시지 만료 날짜 및 시간입니다. |
| iothub-enqueuedtime | IoT Hub에서 디바이스-클라우드 메시지를 수신한 날짜 및 시간입니다. |
| correlation-id| 일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |
| user-id| 메시지의 원본을 지정하는 데 사용되는 ID입니다. |
| iothub-ack| 피드백 메시지 생성기입니다. |
| iothub-connection-device-id| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 deviceId를 포함합니다. |
| iothub-connection-auth-generation-id| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 디바이스의 connectionDeviceGenerationId(디바이스 ID 속성당)를 포함합니다. |
| iothub-connection-auth-method| IoT Hub에서 디바이스-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 디바이스를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. |

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 속성을 포함 해야 합니다.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="create-iot-hub-connection"></a>IoT Hub 연결 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

아직 없는 경우 [Iot Hub를 만듭니다](ingest-data-iot-hub.md#create-an-iot-hub). IoT Hub에 대 한 연결은 [c #](data-connection-iot-hub-csharp.md) 또는 [Python](data-connection-iot-hub-python.md)을 사용 하 여 프로그래밍 방식으로 또는 [Azure Resource Manager 템플릿을](data-connection-iot-hub-resource-manager.md) [Azure Portal](ingest-data-iot-hub.md)통해 관리할 수 있습니다.

> [!Note]
> * `device-to-cloud partitions`개수를 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해 야 합니다.
> * 소비자 그룹은 소비자 당 고유 해야 합니다. Azure 데이터 탐색기 연결 전용 소비자 그룹을 만듭니다. Azure Portal에서 리소스를 찾고로 이동 하 여 `Built-in endpoints` 새 소비자 그룹을 추가 합니다.

## <a name="sending-events"></a>이벤트 전송

장치를 시뮬레이션 하 고 데이터를 생성 하는 [샘플 프로젝트](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

IoT Hub 데이터를 수집 하는 방법에는 여러 가지가 있습니다. 각 방법의 연습은 다음 링크를 참조 하세요.

* [IoT Hub에서 Azure 데이터 탐색기로 데이터 수집](ingest-data-iot-hub.md)
* [C # (미리 보기)을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결 만들기](data-connection-iot-hub-csharp.md)
* [Python (미리 보기)을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결 만들기](data-connection-iot-hub-python.md)
* [Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결 만들기](data-connection-iot-hub-resource-manager.md)