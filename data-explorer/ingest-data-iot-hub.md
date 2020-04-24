---
title: IoT Hub에서 Azure 데이터 탐색기로 데이터 수집
description: 이 문서에서는 IoT Hub에서 Azure 데이터 탐색기로 데이터를 수집(로드)하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1e04b3c2796f8d8814e21763d9a62189b4dc06cb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500386"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>IoT Hub에서 Azure 데이터 탐색기로 데이터 수집 

> [!div class="op_single_selector"]
> * [포털](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-iot-hub-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 빅 데이터 스트리밍 플랫폼 및 IoT 수집 서비스인 IoT Hub의 수집(데이터 로드)을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터베이스 이름 *testdb를*사용하여 [테스트 클러스터 및 데이터베이스를](create-cluster-database-portal.md) 만듭니다.
* 장치를 시뮬레이션하기 위한 [샘플 앱](https://github.com/Azure-Samples/azure-iot-samples-csharp) 및 설명서입니다.
* 샘플 앱을 실행하기 위한 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/).

## <a name="create-an-iot-hub"></a>Iot 허브 만들기

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>IoT 허브에 장치 등록

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 대상 테이블 만들기

이제 IoT Hubs가 데이터를 보낼 Azure 데이터 탐색기에서 테이블을 만듭니다. 클러스터에서 테이블을 만들고 [**데이터베이스는 필수 구성 조건에서**](#prerequisites)프로비전됩니다.

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

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure 데이터 탐색기 테이블을 IoT 허브에 연결

이제 Azure 데이터 탐색기에서 IoT 허브에 연결합니다. 이 연결이 완료되면 iot 허브로 흐르는 데이터는 만든 [대상 테이블로](#create-a-target-table-in-azure-data-explorer)스트리밍됩니다.

1. 도구 모음에서 **알림을** 선택하여 IoT Hub 배포가 성공했는지 확인합니다.

1. 만든 클러스터에서 **데이터베이스를** 선택한 다음 **testdb를**만든 데이터베이스를 선택합니다.
    
    ![테스트 데이터베이스 선택](media/ingest-data-iot-hub/select-database.png)

1. **데이터 수집** 및 **데이터 연결 추가**를 선택합니다. 그런 다음, 다음 정보로 양식을 작성합니다. 완료되면 **만들기를** 선택합니다.

    ![IoT 허브 연결](media/ingest-data-iot-hub/iot-hub-connection.png)

    **데이터 원본**:

    **설정** | **필드 설명**
    |---|---|
    | 데이터 연결 이름 | Azure 데이터 탐색기에서 만들려는 연결의 이름입니다.
    | IoT Hub | IoT Hub 이름 |
    | 공유 액세스 정책 | 공유 액세스 정책의 이름입니다. 읽기 권한이 있어야 합니다. |
    | 소비자 그룹 |  IoT Hub 내장 엔드포인트에 정의된 소비자 그룹 |
    | 이벤트 시스템 속성 | [IoT Hub 이벤트 시스템 속성입니다.](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages) 시스템 속성을 추가할 때 선택한 속성을 포함하도록 테이블 스키마 및 [매핑을](kusto/management/mappings.md) [만들거나](kusto/management/create-table-command.md) [업데이트합니다.](kusto/management/alter-table-command.md) | | | 

    > [!NOTE]
    > 수동 장애 [조치의](/azure/iot-hub/iot-hub-ha-dr#manual-failover)경우 데이터 연결을 다시 만들어야 합니다.

    **대상 테이블**:

    수집된 데이터를 라우팅하기 위한 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 
    이 문서에서는 고정 라우팅을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. 따라서 **내 데이터에 라우팅 정보 포함**을 선택 취소한 상태로 둡니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 테이블 | *TestTable* | **testdb에서**만든 테이블 . |
    | 데이터 형식 | *JSON* | 지원되는 형식은 아브로, CSV, JSON, 멀티 라인 JSON, PSV, SOHSV, SCSV, TSV, TSVE 및 TXT입니다. |
    | 열 매핑 | *TestMapping* | **testdb에서**만든 [매핑은](kusto/management/mappings.md) 들어오는 JSON 데이터를 **testdb의**열 이름 및 데이터 유형에 매핑합니다. JSON, 멀티 라인 JSON 및 AVRO에 필요하고 다른 형식의 경우 선택 사항입니다.|
    | | |

    > [!NOTE]
    > * 동적 라우팅을 사용하려면 **라우팅 정보를 포함하는 내 데이터**를 선택합니다. [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 주석에서 볼 수 있듯이 데이터에는 필수 라우팅 정보가 포함됩니다. 정적 및 동적 속성을 모두 설정하는 경우 동적 속성은 정적 속성을 재정의합니다. 
    > * 데이터 연결을 만든 후 큐에 들어있는 이벤트만 수집됩니다.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>테스트를 위한 샘플 데이터 생성

시뮬레이션된 디바이스 애플리케이션은 IoT 허브의 디바이스 관련 엔드포인트에 연결하고 시뮬레이션된 온도 및 습도 원격 분석을 전송합니다.

1. [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 서 샘플 C# 다운로드하고 ZIP 보관 파일을 추출합니다.

1. 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.cs** 파일을 엽니다.

    `s_connectionString` [장치 등록에서 IoT Hub에](#register-a-device-to-the-iot-hub)대한 장치 연결 문자열로 변수 값을 바꿉입니다. 그런 다음 변경 사항을 **SimulatedDevice.cs** 파일에 저장합니다.

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

앱에서 데이터를 생성하면 이제 IoT 허브에서 클러스터의 테이블로의 데이터 흐름을 확인할 수 있습니다.

1. Azure 포털에서 IoT 허브 아래에서 앱이 실행되는 동안 활동이 급증하는 것을 볼 수 있습니다.

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

    결과 집합:
    
    ![수집된 데이터 결과 표시](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer에는 데이터 수집을 위한 집계(일괄 처리) 정책이 있으며, 이는 수집 프로세스를 최적화하도록 설계되었습니다. 이 정책은 기본적으로 5분 또는 500MB의 데이터로 구성되므로 대기 시간이 발생할 수 있습니다. 집계 옵션에 대한 내용은 [일괄 처리 정책](kusto/management/batchingpolicy.md)을 참조하세요. 
    > * 스트리밍을 지원하도록 테이블을 구성하고 응답 시간에 지연을 제거합니다. [스트리밍 정책을](kusto/management/streamingingestionpolicy.md)참조하십시오. 

## <a name="clean-up-resources"></a>리소스 정리

IoT Hub를 다시 사용하지 않으려는 경우 비용이 발생하지 않도록 **테스트 허브-rg를**정리합니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹**을 선택한 다음, 만든 리소스 그룹을 선택합니다.  

    왼쪽 메뉴가 접혀 있으면 ![[확장] 단추를](media/ingest-data-event-hub/expand.png) 클릭하여 펼칩니다.

   ![삭제할 리소스 그룹 선택](media/ingest-data-event-hub/delete-resources-select.png)

1. **test-resource-group** 아래에서 **리소스 그룹 삭제**를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름(*test-hub-rg*)을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기의 쿼리 데이터](web-query-data.md)
