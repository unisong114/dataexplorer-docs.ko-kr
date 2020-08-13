---
title: Azure 데이터 탐색기 데이터 수집 개요
description: Azure 데이터 탐색기에서 데이터를 수집(로드)할 수 있는 다양한 방법 알아보기
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: d46455fcce2880c4f347d04125a9f6355cb4cffa
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201547"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Azure 데이터 탐색기 데이터 수집 개요 

데이터 수집은 하나 이상의 원본에서 데이터 레코드를 로드 하 여 Azure 데이터 탐색기의 테이블로 데이터를 가져오는 데 사용 되는 프로세스입니다. 수집한 후에는 데이터를 쿼리에 사용할 수 있게 됩니다.

아래 다이어그램에서는 Azure 데이터 탐색기 작업에 대 한 종단 간 흐름을 보여 주고 다른 수집 방법을 보여 줍니다.

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="데이터 수집 및 관리의 개요 구성표":::

데이터 수집을 담당 하는 Azure 데이터 탐색기 데이터 관리 서비스는 다음 프로세스를 구현 합니다.

Azure 데이터 탐색기는 외부 소스에서 데이터를 가져오고 보류 중인 Azure 큐에서 요청을 읽습니다. 데이터가 일괄 처리 되거나 Data Manager로 스트리밍됩니다. 동일한 데이터베이스 및 테이블로 흐르는 일괄 처리 데이터는 수집 처리량에 최적화 되어 있습니다. Azure 데이터 탐색기는 초기 데이터의 유효성을 검사 하 고 필요한 경우 데이터 형식을 변환 합니다. 추가 데이터 조작에는 일치 하는 스키마, 데이터 구성, 인덱싱, 인코딩 및 압축이 포함 됩니다. 데이터는 설정 된 보존 정책에 따라 저장소에 유지 됩니다. 그런 다음 쿼리에 사용할 수 있는 데이터 수집을 엔진에 커밋합니다. Data Manager 

## <a name="supported-data-formats-properties-and-permissions"></a>지원 되는 데이터 형식, 속성 및 사용 권한

* **[지원 되는 데이터 형식](ingestion-supported-formats.md)** 

* 수집 **[속성](ingestion-properties.md)**: 데이터를 수집 하는 방법에 영향을 주는 속성입니다 (예: 태깅, 매핑, 생성 시간).

* **사용 권한**: 데이터를 수집 하려면 프로세스에 [database 수집기 level 권한이](kusto/management/access-control/role-based-authorization.md)필요 합니다. Query와 같은 다른 작업에는 데이터베이스 관리자, 데이터베이스 사용자 또는 테이블 관리자 권한이 필요할 수 있습니다.

## <a name="batching-vs-streaming-ingestion"></a>일괄 처리 및 스트리밍 수집

* 일괄 처리는 데이터 일괄 처리를 수행 하며 높은 수집 처리량에 최적화 되어 있습니다. 이 방법은 선호 되는 가장 성능이 뛰어난 수집 유형입니다. 수집 속성에 따라 데이터가 일괄 처리 됩니다. 그런 다음 데이터의 작은 배치가 병합 되 고 빠른 쿼리 결과를 위해 최적화 됩니다. 수집 [일괄 처리](kusto/management/batchingpolicy.md) 정책은 데이터베이스 또는 테이블에 대해 설정할 수 있습니다. 기본적으로 최대 일괄 처리 값은 5 분, 1000 항목 또는 총 크기인 500 MB입니다.

* [스트리밍 수집은 스트리밍](ingest-data-streaming.md) 원본에서 지속적인 데이터 수집입니다. 스트리밍 수집은 테이블당 작은 데이터 집합에 대 한 거의 실시간 대기 시간을 허용 합니다. 데이터는 처음에 행 저장소로 수집, 열 저장소 범위로 이동 됩니다. 스트리밍 수집은 Azure 데이터 탐색기 클라이언트 라이브러리 또는 지원 되는 데이터 파이프라인 중 하나를 사용 하 여 수행할 수 있습니다. 

## <a name="ingestion-methods-and-tools"></a>수집 방법 및 도구

Azure 데이터 탐색기는 각각 고유한 대상 시나리오를 포함 하는 몇 가지 수집 방법을 지원 합니다. 이러한 방법에는 수집 도구, 다양 한 서비스에 대 한 커넥터 및 플러그 인, 관리 되는 파이프라인, Sdk를 사용 하 여 프로그래밍 방식으로 수집 및 수집에 대 한 직접 액세스

### <a name="ingestion-using-managed-pipelines"></a>관리 되는 파이프라인을 사용 하 여 수집

외부 서비스에서 관리 (제한, 다시 시도, 모니터, 경고 등)를 수행 하려는 조직의 경우 커넥터를 사용 하는 것이 가장 적합 한 솔루션 일 가능성이 높습니다. 큐에 대기된 수집은 큰 데이터 볼륨에 적합합니다. Azure 데이터 탐색기는 다음과 같은 Azure Pipelines 지원 합니다.

* **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: azure storage를 수신 하 고 구독 된 이벤트가 발생할 때 정보를 가져오도록 azure 데이터 탐색기를 업데이트 하는 파이프라인입니다. 자세한 내용은 [Azure Data Explorer에 Azure Blob 수집](ingest-data-event-grid.md)을 참조하세요.

* **[이벤트 허브](https://azure.microsoft.com/services/event-hubs/)**: 서비스에서 Azure 데이터 탐색기로 이벤트를 전송 하는 파이프라인입니다. 자세한 내용은 [이벤트 허브에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)을 참조하세요.

* **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: 지원 되는 IoT 장치에서 Azure 데이터 탐색기로 데이터를 전송 하는 데 사용 되는 파이프라인입니다. 자세한 내용은 [IoT Hub에서 수집](ingest-data-iot-hub.md)을 참조 하세요.

* **Azure Data Factory (ADF)**: Azure의 분석 워크 로드에 대해 완전히 관리 되는 데이터 통합 서비스입니다. Azure Data Factory은 지원 되는 90 이상에 연결 하 여 효율적이 고 복원 력 있는 데이터 전송을 제공 합니다. ADF는 데이터를 준비, 변환 및 강화 하 여 다양 한 종류의 방식으로 모니터링할 수 있는 정보를 제공 합니다. 이 서비스는 정기적인 타임 라인에서 일회성 솔루션으로 사용 하거나 특정 이벤트에 의해 트리거될 수 있습니다. 
  * [Azure 데이터 탐색기 Azure Data Factory와 통합](data-factory-integration.md)합니다.
  * [Azure Data Factory를 사용 하 여 지원 되는 원본에서 Azure 데이터 탐색기로 데이터를 복사](/azure/data-explorer/data-factory-load-data)합니다.
  * [Azure Data Factory 템플릿을 사용 하 여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사](data-factory-template.md)합니다.
  * [Azure Data Factory 명령 작업을 사용 하 여 Azure 데이터 탐색기 제어 명령을 실행](data-factory-command-activity.md)합니다.

### <a name="ingestion-using-connectors-and-plugins"></a>커넥터 및 플러그 인을 사용하여 수집

* **Logstash 태 플러그 인**은 [logstash 태에서 Azure 데이터 탐색기로 데이터 수집](ingest-data-logstash.md)을 참조 하세요.

* **Kafka 커넥터**에서 [Azure 데이터 탐색기로 kafka에서 데이터 수집](ingest-data-kafka.md)을 참조 하세요.

* **[전원 자동화](https://flow.microsoft.com/)**: Azure 데이터 탐색기에 대 한 자동화 된 워크플로 파이프라인입니다. 파워 자동화를 사용 하 여 쿼리를 실행 하 고 쿼리 결과를 트리거로 사용 하 여 사전 설정 작업을 수행할 수 있습니다. [파워 자동화 (미리 보기) 하려면 Azure 데이터 탐색기 커넥터를](flow.md)참조 하세요.

* **Apache Spark 커넥터**: 모든 Spark 클러스터에서 실행할 수 있는 오픈 소스 프로젝트입니다. Azure 데이터 탐색기 및 Spark 클러스터에서 데이터를 이동 하기 위한 데이터 원본 및 데이터 싱크를 구현 합니다. 데이터 기반 시나리오를 대상으로 하는 빠르고 확장 가능한 응용 프로그램을 빌드할 수 있습니다. [Apache Spark에 대 한 Azure 데이터 탐색기 커넥터를](spark-connector.md)참조 하세요.

### <a name="programmatic-ingestion-using-sdks"></a>Sdk를 사용 하 여 프로그래밍 방식 수집

Azure 데이터 탐색기는 쿼리 및 데이터 수집에 사용할 수 있는 SDK를 제공합니다. 프로그래밍 방식 수집은 수집 프로세스 중 및 후에 스토리지 트랜잭션을 최소화하여 수집 비용(COG)을 줄이도록 최적화됩니다.

**사용 가능한 Sdk 및 오픈 소스 프로젝트**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO API](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>도구

* **[한 번의 클릭](ingest-data-one-click.md)** 으로 수집: 광범위 한 소스 형식에서 테이블을 만들고 조정 하 여 데이터를 신속 하 게 수집할 수 있습니다. 한 번의 클릭으로 Azure 데이터 탐색기의 데이터 원본을 기반으로 테이블 및 매핑 구조를 자동으로 제안 합니다. 한 번의 클릭으로 한 번의 수집을 사용 하거나, 데이터를 수집 하는 컨테이너에 대 한 Event Grid를 통해 연속 수집을 정의할 수 있습니다.

* **[LightIngest](lightingest.md)**: Azure 데이터 탐색기에 대 한 임시 데이터 수집을 위한 명령줄 유틸리티입니다. 유틸리티는 로컬 폴더 또는 Azure blob 저장소 컨테이너에서 원본 데이터를 끌어올 수 있습니다.

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto 쿼리 언어 수집 제어 명령

KQL (Kusto Query Language) 명령을 통해 데이터를 엔진에 직접 수집 수 있는 여러 가지 방법이 있습니다. 이 메서드는 데이터 관리 서비스를 무시 하므로 탐색 및 프로토타입 작성에만 적합 합니다. 프로덕션 또는 고용량 시나리오에서는이 방법을 사용 하지 마세요.

  * **인라인**수집: 제어 명령 [입니다.](kusto/management/data-ingestion/ingest-inline.md) 수집 데이터는 명령 텍스트 자체의 일부가 되도록 엔진에 전송 됩니다. 이 메서드는 근간이 테스트 목적으로 사용 됩니다.

  * **쿼리에서 수집**: 제어 명령 [입니다. set,. append,. set 또는-append 또는. set 또는 replace](kusto/management/data-ingestion/ingest-from-query.md) 는 쿼리 또는 명령의 결과로 간접적으로 지정 된 데이터를 사용 하 여 엔진으로 전송 됩니다.

  * **저장소에서 수집 (pull)**: 제어 명령. 엔진으로 수집 하 고, 엔진에서 액세스할 수 있고 명령에 의해 가리키는 외부 저장소 (예: Azure Blob Storage)에 저장 된 데이터를 사용 하 여 데이터를 엔진에 전송 합니다 [.](kusto/management/data-ingestion/ingest-from-storage.md)

## <a name="comparing-ingestion-methods-and-tools"></a>수집 방법 및 도구 비교

| 수집 이름 | 데이터 형식 | 최대 파일 크기 | 스트리밍, 일괄 처리, 직접 | 가장 일반적인 시나리오 | 고려 사항 |
| --- | --- | --- | --- | --- | --- |
| [**한 번의 클릭 수집**](ingest-data-one-click.md) | * sv, JSON | 1gb 압축 되지 않음 (참고 참조)| 직접 수집의 컨테이너, 로컬 파일 및 blob에 대 한 일괄 처리 | 일회용, create table schema, event grid를 사용한 연속 수집 정의, 컨테이너로 대량 수집 (최대 1만 blob) | 1만 blob은 컨테이너에서 무작위로 선택 됩니다.|
| [**LightIngest**](lightingest.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | DM을 통한 일괄 처리 또는 엔진에 직접 수집 |  데이터 마이그레이션, 조정 된 수집 타임 스탬프가 있는 기록 데이터, 대량 수집 (크기 제한 없음)| 대/소문자 구분, 공간 구분 |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure 데이터 팩터리**](kusto/tools/azure-data-factory.md) | [지원 되는 데이터 형식](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | 무제한 * (ADF 제한 당) | 일괄 처리 또는 ADF 별 트리거 | 일반적으로 지원 되지 않는 많은 파일 (일반적으로 지원 되지 않음)에서 perm의 원본에서 클라우드로 90 복사할 수 있는 형식을 지원 합니다. | 수집 시간 |
|[**Azure 데이터 흐름**](kusto/tools/flow.md) | | | | 흐름의 일부로 명령 수집| 높은 성능의 응답 시간이 있어야 합니다. |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [지원 되는 데이터 형식](ingest-data-iot-hub-overview.md#data-format)  | N/A | 일괄 처리, 스트리밍 | Iot 메시지, iot 이벤트, IoT 속성 | |
| [**이벤트 허브**](ingest-data-event-hub-overview.md) | [지원 되는 데이터 형식](ingest-data-event-hub-overview.md#data-format) | N/A | 일괄 처리, 스트리밍 | 메시지, 이벤트 | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [지원 되는 데이터 형식](ingest-data-event-grid-overview.md#data-format) | 1gb 압축 되지 않음 | 일괄 처리 | Azure storage의 연속 수집, Azure storage의 외부 데이터 | 100 KB는 최적의 파일 크기 이며 blob 이름 바꾸기 및 blob 만들기에 사용 됩니다. |
| [**Net Std**](net-standard-ingest-data.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | 일괄 처리, 스트리밍, 직접 | 조직 요구 사항에 따라 사용자 고유의 코드 작성 |
| [**Python**](python-ingest-data.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | 일괄 처리, 스트리밍, 직접 | 조직 요구 사항에 따라 사용자 고유의 코드 작성 |
| [**Node.JS**](node-ingest-data.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조 | 일괄 처리, 스트리밍, 직접 | 조직 요구 사항에 따라 사용자 고유의 코드 작성 |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | 일괄 처리, 스트리밍, 직접 | 조직 요구 사항에 따라 사용자 고유의 코드 작성 |
| [**REST (영문)**](kusto/api/netfx/kusto-ingest-client-rest.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | 일괄 처리, 스트리밍, 직접| 조직 요구 사항에 따라 사용자 고유의 코드 작성 |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | 지원 되는 모든 형식 | 1gb 압축 되지 않음 (참고 참조) | 일괄 처리, 스트리밍, 직접 | 조직 요구 사항에 따라 사용자 고유의 코드 작성 |

> [!Note] 
> 위의 표에서 참조 하는 경우 수집은 최대 파일 크기 5gb를 지원 합니다. 100MB에서 1GB 사이의 파일을 수집하는 것이 좋습니다.

## <a name="ingestion-process"></a>수집 프로세스

요구에 가장 적합 한 수집 방법을 선택한 후에는 다음 단계를 수행 합니다.

1. **보존 정책 설정**

    Azure 데이터 탐색기의 테이블에 데이터 수집는 테이블의 유효 보존 정책이 적용 됩니다. 테이블에 명시적으로 설정되지 않은 경우 유효 보존 정책은 데이터베이스의 보존 정책에서 파생됩니다. 핫 보존은 클러스터 크기 및 보존 정책에 대 한 기능입니다. 사용 가능한 공간 보다 더 많은 데이터를 수집 첫 번째 데이터가 콜드 보존으로 강제 적용 됩니다.
    
    데이터베이스의 보존 정책이 필요에 적합 한지 확인 합니다. 그렇지 않으면 테이블 수준에서 명시적으로 재정의합니다. 자세한 내용은 [보존 정책](kusto/management/retentionpolicy.md)을 참조 하세요. 
    
1. **테이블 만들기**

    데이터를 수집 하기 위해 테이블을 미리 만들어야 합니다. 다음 옵션 중 하나를 사용합니다.
   * [명령을 사용 하 여](kusto/management/create-table-command.md)테이블을 만듭니다. 
   * [한 번의 클릭](one-click-ingestion-new-table.md)수집을 사용 하 여 테이블을 만듭니다.

    > [!Note]
    > 레코드가 불완전하거나 필수 데이터 형식으로 필드를 구문 분석할 수 없는 경우, 해당 테이블 열에는 Null 값이 채워집니다.

1. **스키마 매핑 만들기**

    [스키마 매핑은](kusto/management/mappings.md) 원본 데이터 필드를 대상 테이블 열에 바인딩하는 데 도움이 됩니다. 매핑을 사용 하면 정의 된 특성을 기반으로 여러 원본에서 동일한 테이블로 데이터를 가져올 수 있습니다. 행 기반 (CSV, JSON 및 AVRO) 및 열 지향 (Parquet)의 여러 가지 매핑 유형이 지원 됩니다. 대부분의 경우 [테이블에 대 한 매핑을 미리 만들고](kusto/management/create-ingestion-mapping-command.md) 수집 명령 매개 변수에서 참조할 수도 있습니다.

1. **업데이트 정책 설정** (선택 사항)

   일부 데이터 형식 매핑 (Parquet, JSON 및 Avro)은 간단 하 고 유용한 수집 시간 변환을 지원 합니다. 수집 시 시나리오에 더 복잡 한 처리가 필요한 경우 Kusto 쿼리 언어 명령을 사용 하 여 간단한 처리를 허용 하는 업데이트 정책을 사용 합니다. 업데이트 정책은 원래 테이블의 수집 데이터에 대해 추출 및 변환을 자동으로 실행 하 고 결과 데이터를 하나 이상의 대상 테이블로 수집 합니다. [업데이트 정책을](kusto/management/update-policy.md)설정 합니다.



## <a name="next-steps"></a>다음 단계

* [지원 되는 데이터 형식](ingestion-supported-formats.md)
* [지원 되는 수집 속성](ingestion-properties.md)
