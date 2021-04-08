---
title: Azure Data Explorer 데이터 수집 개요
description: Azure 데이터 탐색기에서 데이터를 수집(로드)할 수 있는 다양한 방법 알아보기
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: bb5e6823527a40d817eb2c9945af5bc951712363
ms.sourcegitcommit: 294130a8c44d26e62146dcc52a88e56d0f98a151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106376371"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Azure Data Explorer 데이터 수집 개요 

데이터 수집은 하나 이상의 원본에서 데이터 레코드를 로드하여 Azure Data Explorer의 테이블로 데이터를 가져오는 데 사용되는 프로세스입니다. 수집한 후에는 데이터를 쿼리에 사용할 수 있게 됩니다.

아래 다이어그램은 Azure Data Explorer의 엔드투엔드 작업 흐름과 여러 가지 수집 방법을 보여줍니다.

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="데이터 수집 및 관리의 개요 구성표":::

데이터 수집을 담당하는 Azure Data Explorer 데이터 관리 서비스는 다음과 같은 프로세스를 구현합니다.

Azure Data Explorer는 외부 원본에서 데이터를 끌어오고 보류 중인 Azure 큐에서 요청을 읽습니다. 데이터는 일괄 처리되거나 Data Manager로 스트리밍됩니다. 동일한 데이터베이스와 테이블로 흐르는 일괄 처리 데이터는 수집 처리량에 최적화됩니다. Azure Data Explorer는 초기 데이터의 유효성을 검사하고 필요한 경우 데이터 형식을 변환합니다. 그 외에도 스키마 매칭, 데이터 구성, 데이터 인덱싱, 데이터 인코딩, 데이터 압축 등의 데이터 조작이 더 있습니다. 데이터는 설정된 보존 정책에 따라 스토리지에 보존됩니다. 그러면 Data Manager는 쿼리에 사용할 수 있는 엔진에 데이터를 수집합니다. 

## <a name="supported-data-formats-properties-and-permissions"></a>지원되는 데이터 형식, 속성 및 권한

* **[지원되는 데이터 형식](ingestion-supported-formats.md)** 

* **[수집 속성](ingestion-properties.md)** : 데이터 수집 방법에 영향을 주는 속성입니다(예: 태그 지정, 매핑, 생성 시간).

* **권한** 데이터를 수집하려면 프로세스에서 [데이터베이스 수집기 수준 권한](kusto/management/access-control/role-based-authorization.md)이 필요합니다. 쿼리와 같은 다른 작업에는 데이터베이스 관리자, 데이터베이스 사용자 또는 테이블 관리자 권한이 필요할 수 있습니다.

## <a name="batching-vs-streaming-ingestion"></a>일괄 처리 수집과 스트리밍 수집의 차이

* 일괄 처리 수집은 데이터 일괄 처리를 수행하며 대량 수집에 최적화되었습니다. 선호도가 높고 가장 성능이 뛰어난 수집 방법입니다. 수집 속성에 따라 데이터가 일괄 처리됩니다. 작은 데이터 일괄 처리는 병합되고, 빠른 쿼리 결과를 위해 최적화됩니다. 데이터베이스 또는 테이블에서 [수집 일괄 처리](kusto/management/batchingpolicy.md) 정책을 설정할 수 있습니다. 기본적으로 최대 일괄 처리 값은 5분, 1000개 항목 또는 전체 크기 1GB입니다.

* [스트리밍 수집](ingest-data-streaming.md)은 스트리밍 원본에서 지속적으로 데이터를 수집하는 것입니다. 스트리밍 수집은 테이블마다 소량의 데이터 세트에 대해 거의 실시간 대기 시간을 허용합니다. 데이터는 처음에 행 저장소에 수집되고, 이후에 열 저장소 범위로 이동됩니다. 스트리밍 수집은 Azure Data Explorer 클라이언트 라이브러리 또는 지원되는 데이터 파이프라인 중 하나를 사용하여 수행할 수 있습니다. 

## <a name="ingestion-methods-and-tools"></a>수집 방법 및 도구

Azure Data Explorer는 여러 가지 수집 방법을 지원하며, 방법마다 고유의 시나리오가 있습니다. 이러한 방법에는 수집 도구, 다양한 서비스에 대한 커넥터 및 플러그 인, 관리형 파이프라인, SDK를 사용하는 프로그래밍 방식 수집, 그리고 직접 액세스 수집이 있습니다.

### <a name="ingestion-using-managed-pipelines"></a>관리형 파이프라인을 사용하여 수집

관리(제한, 다시 시도, 모니터링 및 경고)를 외부 서비스에 맡기려는 조직의 경우 커넥터를 사용하는 것이 가장 적합한 솔루션일 가능성이 높습니다. 큐에 대기된 수집은 큰 데이터 볼륨에 적합합니다. Azure Data Explorer는 다음과 같은 Azure Pipelines를 지원합니다.

* **[Event Grid](https://azure.microsoft.com/services/event-grid/)** : Azure 스토리지를 수신 대기하다가 구독한 이벤트가 발생하면 정보를 끌어오도록 Azure Data Explorer를 업데이트하는 파이프라인입니다. 자세한 내용은 [Azure Data Explorer에 Azure Blob 수집](ingest-data-event-grid.md)을 참조하세요.

* **[Event Hub](https://azure.microsoft.com/services/event-hubs/)** : 서비스에서 Azure Data Explorer로 이벤트를 전송하는 파이프라인입니다. 자세한 내용은 [이벤트 허브에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)을 참조하세요.

* **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)** : 지원되는 IoT 디바이스에서 Azure Data Explorer로 데이터를 전송하는 데 사용되는 파이프라인입니다. 자세한 내용은 [IoT Hub에서 수집](ingest-data-iot-hub.md)을 참조하세요.

* **ADF(Azure Data Factory)** : Azure의 분석 워크로드에 사용되는 완전 관리형 데이터 통합 서비스입니다. Azure Data Factory는 90개 이상의 지원되는 원본에 연결하여 효율적이고 복원력 있는 데이터 전송을 제공합니다. ADF는 데이터를 준비, 변환 및 강화하여 다양한 방법으로 모니터링할 수 있는 인사이트를 제공합니다. 이 서비스는 일회성 솔루션으로, 주기적으로 또는 특정 이벤트 발생 시 트리거되도록 사용할 수 있습니다. 
  * [Azure Data Explorer를 Azure Data Factory와 통합](data-factory-integration.md)
  * [Azure Data Factory를 사용하여 지원되는 원본에서 Azure Data Explorer로 데이터를 복사](./data-factory-load-data.md)
  * [Azure Data Factory 템플릿을 사용하여 데이터베이스에서 Azure Data Explorer로 대량 복사](data-factory-template.md)
  * [Azure Data Factory 명령 작업을 사용하여 Azure Data Explorer 제어 명령 실행](data-factory-command-activity.md)

### <a name="ingestion-using-connectors-and-plugins"></a>커넥터 및 플러그 인을 사용하여 수집

* **Logstash 플러그 인** - [Logstash에서 Azure Data Explorer로 데이터 수집](ingest-data-logstash.md)을 참조하세요.

* **Kafka 커넥터** - [Kafka에서 Azure Data Explorer로 데이터 수집](ingest-data-kafka.md)을 참조하세요.

* **[Power Automate](https://flow.microsoft.com/)** : Azure Data Explorer에 대한 자동화된 워크플로 파이프라인입니다. Power Automate를 사용하여 쿼리를 실행하고, 쿼리 결과를 트리거로 사용하여 사전 설정 작업을 수행할 수 있습니다. [Power Automate용 Azure Data Explorer 커넥터(미리 보기)](flow.md)를 참조하세요.

* **Apache Spark 커넥터**:  모든 Spark 클러스터에서 실행할 수 있는 오픈 소스 프로젝트입니다. Azure Data Explorer 및 Spark 클러스터에서 데이터를 이동하기 위한 데이터 원본 및 데이터 싱크를 구현합니다. 데이터 기반 시나리오를 대상으로 하는 빠르고 확장성 있는 애플리케이션을 빌드할 수 있습니다. [Apache Spark용 Azure Data Explorer 커넥터](spark-connector.md)를 참조하세요.

### <a name="programmatic-ingestion-using-sdks"></a>SDK를 사용하는 프로그래밍 방식 수집

Azure 데이터 탐색기는 쿼리 및 데이터 수집에 사용할 수 있는 SDK를 제공합니다. 프로그래밍 방식 수집은 수집 프로세스 중 및 후에 스토리지 트랜잭션을 최소화하여 수집 비용(COG)을 줄이도록 최적화됩니다.

**사용 가능한 SDK 및 오픈 소스 프로젝트**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO SDK](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>도구

* **[원클릭 수집](ingest-data-one-click.md)** : 다양한 형식의 원본에서 테이블을 만들고 조정하여 신속하게 데이터를 수집할 수 있습니다. 원클릭 수집은 Azure Data Explorer의 데이터 원본을 기반으로 테이블 및 매핑 구조를 자동으로 제안합니다. 원클릭 수집은 일회성 수집에 사용하거나, 데이터가 수집된 컨테이너의 Event Grid를 통해 지속적인 수집을 정의하는 데 사용할 수 있습니다.

* **[LightIngest](lightingest.md)** : 임시로 데이터를 Azure Data Explorer로 수집하는 데 사용되는 명령줄 유틸리티입니다. 이 유틸리티는 로컬 폴더 또는 Azure BLOB 스토리지 컨테이너에서 원본 데이터를 끌어올 수 있습니다.

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto 쿼리 언어 수집 제어 명령

KQL(Kusto 쿼리 언어) 명령을 통해 데이터를 엔진에 직접 수집할 수 있는 여러 가지 방법이 있습니다. 이 방법은 데이터 관리 서비스를 우회하므로 탐색 및 프로토타입 작성에만 적합합니다. 프로덕션 또는 대량 시나리오에는 이 방법을 사용하지 마세요.

  * **인라인 수집**:  제어 명령 [.ingest inline](kusto/management/data-ingestion/ingest-inline.md)은 엔진으로 전송되고, 수집할 데이터는 명령 텍스트 자체에 포함됩니다. 이 방법은 임시 테스트 용도로 사용됩니다.

  * **쿼리에서 수집**: 제어 명령 [.set, .append, .set-or-append 또는 .set-or-replace](kusto/management/data-ingestion/ingest-from-query.md)는 엔진으로 전송되고, 데이터는 쿼리 또는 명령의 결과로 간접적으로 지정됩니다.

  * **스토리지에서 수집(끌어오기)** : 제어 명령 [.ingest into](kusto/management/data-ingestion/ingest-from-storage.md)는 엔진으로 전송되고, 데이터는 엔진에서 액세스할 수 있고 명령이 가리키는 외부 스토리지(예: Azure Blob Storage)에 저장됩니다.

## <a name="comparing-ingestion-methods-and-tools"></a>수집 방법 및 도구 비교

| 수집 이름 | 데이터 형식 | 최대 파일 크기 | 스트리밍, 일괄 처리, 직접 | 가장 일반적인 시나리오 | 고려 사항 |
| --- | --- | --- | --- | --- | --- |
| [**원클릭 수집**](ingest-data-one-click.md) | *sv, JSON | 1GB 미압축(참고 사항 참조)| 컨테이너, 로컬 파일 및 BLOB에 직접 수집으로 일괄 처리 | 일회성, 테이블 스키마 만들기, 이벤트 그리드를 사용하여 지속적인 수집 정의, 컨테이너로 대량 수집(최대 10,000개 BLOB) | 컨테이너에서 BLOB 10,000개를 무작위로 선택|
| [**LightIngest**](lightingest.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | DM을 통해 일괄 처리 또는 엔진에 직접 수집 |  데이터 마이그레이션, 수집 타임스탬프가 조정된 기록 데이터, 대량 수집(크기 제한 없음)| 대/소문자 구분, 공간 구분 |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX-Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**ADF(Azure Data Factory)**](./data-factory-integration.md) | [지원되는 데이터 형식](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | 무제한 *(ADF마다 제한) | 일괄 처리 또는 ADF마다 트리거 | 일반적으로 지원되지 않는 형식 지원, 대량의 파일, 90개가 넘는 원본의 데이터를 온-프레미스에서 클라우드로 복사 가능 | 이 메서드는 데이터가 수집될 때까지 상대적으로 더 많은 시간이 걸립니다. ADF는 모든 데이터를 메모리에 업로드한 다음, 수집을 시작합니다. |
|[ **Azure Data Flow**](./flow.md) | | | | 수집 명령이 흐름에 포함| 응답 속도가 빨라야 함 |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [지원되는 데이터 형식](ingest-data-iot-hub-overview.md#data-format)  | 해당 없음 | 일괄 처리, 스트리밍 | IoT 메시지, IoT 이벤트, IoT 속성 | |
| [**이벤트 허브**](ingest-data-event-hub-overview.md) | [지원되는 데이터 형식](ingest-data-event-hub-overview.md#data-format) | 해당 없음 | 일괄 처리, 스트리밍 | 메시지, 이벤트 | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [지원되는 데이터 형식](ingest-data-event-grid-overview.md#data-format) | 1GB 미압축 | 일괄 처리 | Azure 스토리지에서 지속적으로 수집, Azure 스토리지의 외부 데이터 | 최적의 파일 크기는 100KB로, BLOB 이름 바꾸기 및 BLOB 만들기에 사용 |
| [**.NET SDK**](./net-sdk-ingest-data.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접 | 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |
| [**Python**](python-ingest-data.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접 | 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |
| [**Node.js**](node-ingest-data.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접 | 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접 | 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접| 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | 모든 형식 지원 | 1GB 미압축(참고 사항 참조) | 일괄 처리, 스트리밍, 직접 | 조직의 요구 사항에 맞게 사용자 고유의 코드 작성 |

> [!Note] 
> 위의 표를 참조할 때 수집 가능한 최대 파일 크기는 4GB입니다. 100MB에서 1GB 사이의 파일을 수집하는 것이 좋습니다.

## <a name="ingestion-process"></a>수집 프로세스

요구 사항에 가장 적합한 수집 방법을 선택한 후에는 다음 단계를 수행합니다.

1. **보존 정책 설정**

    Azure Data Explorer의 테이블에 수집되는 데이터에는 테이블의 유효 보존 정책이 적용됩니다. 테이블에 명시적으로 설정되지 않은 경우 유효 보존 정책은 데이터베이스의 보존 정책에서 파생됩니다. 핫 보존은 클러스터 크기 및 보존 정책에 대한 함수입니다. 사용 가능한 공간보다 더 많은 데이터를 수집하면 첫 번째 데이터가 콜드 보존으로 강제 적용됩니다.
    
    데이터베이스의 보존 정책이 요구 사항에 적합해야 합니다. 그렇지 않으면 테이블 수준에서 명시적으로 재정의합니다. 자세한 내용은 [보존 정책](kusto/management/retentionpolicy.md)을 참조하세요. 
    
1. **테이블 만들기**

    데이터를 수집하려면 먼저 테이블을 만들어야 합니다. 다음 옵션 중 하나를 사용합니다.
   * [명령](kusto/management/create-table-command.md)을 사용하여 테이블을 만듭니다. 
   * [원클릭 수집](one-click-ingestion-new-table.md)을 사용하여 테이블을 만듭니다.

    > [!Note]
    > 레코드가 불완전하거나 필수 데이터 형식으로 필드를 구문 분석할 수 없는 경우, 해당 테이블 열에는 Null 값이 채워집니다.

1. **스키마 매핑 만들기**

    [스키마 매핑](kusto/management/mappings.md)은 원본 데이터 필드를 대상 테이블 열에 바인딩하는 데 도움이 됩니다. 매핑을 사용하면 정의된 특성에 따라 여러 원본의 데이터를 동일한 테이블로 가져올 수 있습니다. 행 기반(CSV, JSON 및 AVRO) 및 열 기반(Parquet)의 여러 가지 매핑 형식이 지원됩니다. 대부분의 방법에서 매핑을 [테이블에 미리 만들고](kusto/management/create-ingestion-mapping-command.md) 수집 명령 매개 변수에서 참조할 수도 있습니다.

1. **업데이트 정책 설정**(선택 사항)

   일부 데이터 형식 매핑(Parquet, JSON 및 Avro)은 간단하면서도 유용한 수집 시간 변환을 지원합니다. 데이터를 수집할 때 좀 더 복잡한 처리가 필요한 시나리오인 경우 Kusto 쿼리 언어 명령을 사용하여 간단하게 처리할 수 있는 업데이트 정책을 사용합니다. 업데이트 정책은 원래 테이블에 수집된 데이터에 대해 추출 및 변환을 자동으로 실행하고, 그 결과로 얻은 데이터를 하나 이상의 대상 테이블에 수집합니다. [업데이트 정책](kusto/management/update-policy.md)을 설정하세요.



## <a name="next-steps"></a>다음 단계

* [지원되는 데이터 형식](ingestion-supported-formats.md)
* [지원되는 수집 속성](ingestion-properties.md)
