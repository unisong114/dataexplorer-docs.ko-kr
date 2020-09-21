---
title: IoT Hub에서 Azure 데이터 탐색기로 데이터 수집
description: 이 문서에서는 IoT Hub에서 Azure 데이터 탐색기로 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/08/2020
ms.openlocfilehash: 47fce36f598c334c5e372ccb7bc44d21bd9ff94f
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832799"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>IoT Hub에서 Azure 데이터 탐색기로 데이터 수집 

> [!div class="op_single_selector"]
> * [포털](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

이 문서에서는 빅 데이터 스트리밍 플랫폼과 IoT 수집 서비스인 IoT Hub에서 Azure 데이터 탐색기으로 데이터를 수집 하는 방법을 보여 줍니다.

IoT Hub에서 Azure 데이터 탐색기에 수집에 대 한 일반 정보는 [IoT Hub에 연결](ingest-data-iot-hub-overview.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터베이스 이름이 *testdb*인 [테스트 클러스터 및 데이터베이스를](create-cluster-database-portal.md) 만듭니다.
* 장치 시뮬레이션에 대 한 [샘플 앱](https://github.com/Azure-Samples/azure-iot-samples-csharp) 및 설명서입니다.
* 샘플 앱을 실행하기 위한 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/).

## <a name="create-an-iot-hub"></a>Iot Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>장치를 IoT Hub에 등록

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 대상 테이블 만들기

이제 Azure 데이터 탐색기에서 IoT Hub가 데이터를 보낼 테이블을 만듭니다. 클러스터 및 [**필수 구성 요소**](#prerequisites)에서 프로 비전 된 데이터베이스에서 테이블을 만듭니다.

1. Azure Portal에서 클러스터로 이동한 후 **쿼리**를 선택합니다.

    ![포털의 ADX 쿼리](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 수집된 데이터를 받을 테이블(TestTable)을 만듭니다.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![쿼리 만들기 실행](media/ingest-data-iot-hub/run-create-query.png)

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 들어오는 JSON 데이터를 테이블(TestTable)의 열 이름과 데이터 형식에 매핑합니다.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure 데이터 탐색기 테이블을 IoT hub에 연결

이제 Azure 데이터 탐색기에서 IoT Hub에 연결 합니다. 이 연결이 완료 되 면 [사용자가 만든 대상 테이블로](#create-a-target-table-in-azure-data-explorer)iot hub로 이동 하는 데이터가 전달 됩니다.

1. 도구 모음에서 **알림** 을 선택 하 여 IoT Hub 배포가 성공적으로 수행 되었는지 확인 합니다.

1. 만든 클러스터에서 **데이터베이스** 를 선택한 다음 **testdb**를 만든 데이터베이스를 선택 합니다.
    
    ![테스트 데이터베이스 선택](media/ingest-data-iot-hub/select-database.png)

1. **데이터 수집** 및 **데이터 연결 추가**를 선택합니다.

    :::image type="content" source="media/ingest-data-iot-hub/iot-hub-connection.png" alt-text="IoT Hub에 대 한 데이터 연결 만들기-Azure 데이터 탐색기":::

### <a name="create-a-data-connection"></a>데이터 연결 만들기

1. 다음 정보로 양식을 작성합니다. 
    
    :::image type="content" source="media/ingest-data-iot-hub/data-connection-pane.png" alt-text="IoT Hub의 데이터 연결 창-Azure 데이터 탐색기":::

    **설정** | **필드 설명**
    |---|---|
    | 데이터 연결 이름 | Azure 데이터 탐색기에서 만들려는 연결의 이름
    | 구독 |  이벤트 허브 리소스가 있는 구독 ID입니다.  |
    | IoT Hub | IoT Hub 이름 |
    | 공유 액세스 정책 | 공유 액세스 정책의 이름입니다. 읽기 권한이 있어야 합니다. |
    | 소비자 그룹 |  IoT Hub 기본 제공 끝점에 정의 된 소비자 그룹 |
    | 이벤트 시스템 속성 | [IoT Hub 이벤트 시스템 속성](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages)입니다. 시스템 속성을 추가 하는 경우 선택한 속성을 포함 하도록 테이블 스키마 및 [매핑을](kusto/management/mappings.md) [만들거나](kusto/management/create-table-command.md) [업데이트](kusto/management/alter-table-command.md) 합니다. | | | 

#### <a name="target-table"></a>대상 테이블

수집된 데이터를 라우팅하기 위한 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 이 문서에서는 고정 라우팅을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. 이벤트 허브 메시지가 데이터 라우팅 정보를 포함 하는 경우이 라우팅 정보는 기본 설정을 재정의 합니다.

1. 다음 라우팅 설정을 입력 합니다.
    
    :::image type="content" source="media/ingest-data-iot-hub/default-routing-settings.png" alt-text="기본 라우팅 속성-IoT Hub-Azure 데이터 탐색기":::

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 테이블 이름 | *TestTable* | **Testdb**에서 만든 테이블입니다. |
    | 데이터 형식 | *JSON* | 지원 되는 형식은 Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO 및 W3CLOG입니다.|
    | 매핑 | *TestMapping* | **Testdb**에서 만든 [매핑으로](kusto/management/mappings.md) , 들어오는 데이터를 **testdb**의 열 이름 및 데이터 형식에 매핑합니다. JSON, 여러 줄 JSON 및 AVRO에 필요 하 고 다른 형식의 경우 선택적입니다.|
    | | |

    > [!WARNING]
    > [수동 장애 조치 (failover)](/azure/iot-hub/iot-hub-ha-dr#manual-failover)의 경우 데이터 연결을 다시 만들어야 합니다.
    
    > [!NOTE]
    > * 모든 **기본 라우팅 설정을**지정할 필요는 없습니다. 부분 설정도 허용 됩니다.
    > * 데이터 연결을 만든 후에 큐에 넣은 이벤트만 수집 됩니다.

1. **만들기**를 선택합니다.

### <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

> [!Note]
> * 시스템 속성은 단일 레코드 이벤트에 대해 지원 됩니다.
> * `csv`매핑의 경우 레코드의 시작 부분에 속성이 추가 됩니다. `json`매핑의 경우 드롭다운 목록에 표시 되는 이름에 따라 속성이 추가 됩니다.

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 [시스템 속성](ingest-data-iot-hub-overview.md#system-properties) 을 포함 해야 합니다.

## <a name="generate-sample-data-for-testing"></a>테스트용 샘플 데이터 생성

시뮬레이션된 디바이스 애플리케이션은 IoT 허브의 디바이스 관련 엔드포인트에 연결하고 시뮬레이션된 온도 및 습도 원격 분석을 전송합니다.

1. [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 서 샘플 C# 다운로드하고 ZIP 보관 파일을 추출합니다.

1. 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.cs** 파일을 엽니다.

    변수 값을 `s_connectionString` [장치 등록에서 IoT Hub](#register-a-device-to-the-iot-hub)장치 연결 문자열로 바꿉니다. 그런 다음 변경 사항을 **SimulatedDevice.cs** 파일에 저장합니다.

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 패키지를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

    다음 스크린샷에서는 시뮬레이션된 디바이스 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>데이터 흐름 검토

이제 데이터를 생성 하는 앱을 통해 IoT hub에서 클러스터의 테이블로 데이터 흐름을 확인할 수 있습니다.

1. Azure Portal의 IoT hub에는 앱이 실행 되는 동안 활동이 급증 하는 것을 볼 수 있습니다.

    ![IoT Hub 메트릭](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. 현재까지 데이터베이스로 전송된 메시지의 수를 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```Kusto
    TestTable
    | count
    ```

1. 메시지 내용을 확인하려면 다음 쿼리를 실행합니다.

    ```Kusto
    TestTable
    ```

    결과 집합은 다음과 같습니다.
    
    ![수집 데이터 결과 표시](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer에는 데이터 수집을 위한 집계(일괄 처리) 정책이 있으며, 이는 수집 프로세스를 최적화하도록 설계되었습니다. 이 정책은 기본적으로 5 분 또는 500 MB의 데이터로 구성 되므로 대기 시간이 길어질 수 있습니다. 집계 옵션에 대한 내용은 [일괄 처리 정책](kusto/management/batchingpolicy.md)을 참조하세요. 
    > * 스트리밍을 지원 하도록 테이블을 구성 하 고 응답 시간에서 지연 시간을 제거 합니다. [스트리밍 정책](kusto/management/streamingingestionpolicy.md)을 참조 하세요. 

## <a name="clean-up-resources"></a>리소스 정리

IoT Hub를 다시 사용 하지 않으려는 경우에는 리소스 그룹을 정리 하 여 비용을 발생 시 키 지 않도록 합니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹**을 선택한 다음, 만든 리소스 그룹을 선택합니다.  

    왼쪽 메뉴가 접혀 있으면 ![[확장] 단추를](media/ingest-data-event-hub/expand.png) 클릭하여 펼칩니다.

   ![삭제할 리소스 그룹 선택](media/ingest-data-iot-hub/delete-resources-select.png)

1. **test-resource-group** 아래에서 **리소스 그룹 삭제**를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
