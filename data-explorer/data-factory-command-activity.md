---
title: Azure 데이터 팩터리 에서 Azure 데이터 탐색기 제어 명령 사용
description: 이 항목에서는 Azure 데이터 팩터리에서 Azure 데이터 탐색기 제어 명령을 사용합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: d59e77af1cebe9edb1c026a6dc557facec958917
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497370"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure 데이터 팩터리 명령 작업을 사용하여 Azure 데이터 탐색기 제어 명령 실행

[Azure 데이터](/azure/data-factory/) 팩터리(ADF)는 데이터에 대한 활동 조합을 수행할 수 있는 클라우드 기반 데이터 통합 서비스입니다. ADF를 사용하여 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위한 데이터 기반 워크플로를 만듭니다. **Azure 데이터 팩터리의 Azure 데이터 탐색기 명령** 작업을 사용하면 ADF 워크플로 내에서 Azure 데이터 탐색기 제어 명령을 실행할 수 [있습니다.](kusto/concepts/index.md#control-commands) 이 문서에서는 조회 활동및 Azure Data Explorer 명령 활동을 포함하는 ForEach 활동이 포함된 파이프라인을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md)
* 데이터 원본입니다.
* [데이터 팩토리](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>새 파이프라인 만들기

1. 연필 **작성** 도구를 선택합니다. 
1. 드롭다운에서 파이프라인을 **+** 선택한 다음 클릭하여 새 **파이프라인을** 만듭니다.

   ![새 파이프라인 만들기](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>조회 활동 만들기

[조회 활동은](/azure/data-factory/control-flow-lookup-activity) Azure Data Factory에서 지원하는 모든 데이터 원본에서 데이터 집합을 검색할 수 있습니다. 조회 활동의 출력은 ForEach 또는 기타 활동에서 사용할 수 있습니다.

1. **활동** 창에서 **일반**에서 **조회** 활동을 선택합니다. 오른쪽의 메인 캔버스에 드래그하여 놓습니다.
 
    ![조회 활동 선택](media/data-factory-command-activity/select-activity.png)

1. 이제 캔버스에 만든 조회 활동이 포함됩니다. 캔버스 아래의 탭을 사용하여 관련 매개 변수를 변경합니다. **일반적으로**활동의 이름을 바꿉니다. 

    ![조회 활동 편집](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > 빈 캔버스 영역을 클릭하여 파이프라인 속성을 봅니다. **일반** 탭을 사용하여 파이프라인이름을 바꿉니다. 파이프라인이름은 *파이프라인-4-docs입니다.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>조회 활동에서 Azure 데이터 탐색기 데이터 집합 만들기

1. **설정에서**미리 만든 Azure 데이터 탐색기 **원본 데이터 집합을**선택하거나 **+ New를** 선택하여 새 데이터 집합을 만듭니다.
 
    ![조회 설정에서 데이터 집합 추가](media/data-factory-command-activity/lookup-settings.png)

1. **새 데이터 집합** 창에서 Azure 데이터 **탐색기(Kusto)** 데이터 집합을 선택합니다. 새 데이터 집합을 추가하려면 **계속을** 선택합니다.

   ![새 데이터 집합 선택](media/data-factory-command-activity/select-new-dataset.png) 

1. 새 Azure 데이터 탐색기 데이터 집합 매개 변수는 **설정**에 표시됩니다. 매개 변수를 업데이트하려면 **편집을**선택합니다.

    ![Azure 데이터 탐색기 데이터 집합을 사용 하 고 조회 설정](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. **AzureDataExplorerTable** 새 탭이 기본 캔버스에서 열립니다. 
    * **일반을** 선택하고 데이터 집합 이름을 편집합니다. 
    * 데이터 집합 속성을 편집하려면 **연결을** 선택합니다. 
    * 드롭다운에서 **연결된 서비스를** 선택하거나 **+ 새로** 만들기를 선택하여 새 연결된 서비스를 만듭니다.

    ![Azure 데이터 탐색기 데이터 집합 속성 편집](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 새 연결된 서비스를 만들 때 **새 연결 서비스(Azure 데이터 탐색기)** 페이지가 열립니다.

    ![ADX 새로운 연결 서비스](media/data-factory-command-activity/adx-new-linked-service.png)

   * Azure 데이터 탐색기 연결된 서비스의 **이름을** 선택합니다. 필요한 경우 **설명을** 추가합니다.
   * **통합 런타임을 통한 연결에서**필요한 경우 현재 설정을 변경합니다. 
   * **계정 선택 방법에서** 다음 두 가지 방법 중 하나를 사용하여 클러스터를 선택합니다. 
        * Azure **구독에서** 라디오 단추를 선택하고 **Azure 구독** 계정을 선택합니다. 그런 다음 **클러스터**를 선택합니다. 드롭다운은 사용자에 속한 클러스터만 나열됩니다.
        * 대신 수동으로 라디오 **단추 입력을** 선택하고 **끝점(클러스터** URL)을 입력합니다.
    * **테넌트를**지정합니다.
    * **서비스 주체 ID를 입력합니다.** 주 ID에는 사용 중인 명령에서 요구하는 권한 수준에 따라 적절한 사용 권한이 있어야 합니다.
    * **서비스 주체 키** 단추를 선택하고 서비스 **주체 키를 입력합니다.**
    * 드롭다운 메뉴에서 **데이터베이스를** 선택합니다. 또는 **편집** 확인란을 선택하고 데이터베이스 이름을 입력합니다.
    * 만든 연결된 서비스 연결을 테스트하려면 **테스트 연결을** 선택합니다. 설정에 연결할 수 있으면 녹색 확인 표시 **연결이 성공하면** 나타납니다.
    * 연결된 서비스 만들기를 완료하려면 **완료를** 선택합니다.

1. 연결된 서비스를 설정한 후 **AzureDataExplorerTable** > **연결에서** **테이블** 이름을 추가합니다. **데이터 미리 보기를**선택하여 데이터가 제대로 표시되는지 확인합니다.

   이제 데이터 집합이 준비되었으며 파이프라인을 계속 편집할 수 있습니다.

### <a name="add-a-query-to-your-lookup-activity"></a>조회 활동에 쿼리 추가

1. **파이프라인-4-docs** > **설정에서 다음과** 같은 **쿼리** 텍스트 상자에 쿼리를 추가합니다.

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 필요에 따라 **쿼리 시간 시간 또는** **잘림 없음** 및 첫 번째 행 **만** 속성을 변경합니다. 이 흐름에서는 기본 **쿼리 시간 시간을** 유지하고 확인란을 선택 취소합니다. 

    ![조회 활동의 최종 설정](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>각 활동 만들기 

[For-Each](/azure/data-factory/control-flow-for-each-activity) 활동은 컬렉션을 반복하고 루프에서 지정된 활동을 실행하는 데 사용됩니다. 

1. 이제 파이프라인에 For-Each 활동을 추가합니다. 이 활동은 조회 활동에서 반환된 데이터를 처리합니다. 
    * **활동** 창에서 반복 **& 조건부에서** **ForEach** 활동을 선택하고 캔버스에 끌어 놓습니다.
    * 조회 활동의 출력과 캔버스의 ForEach 활동의 입력 간에 선을 그려 연결합니다.

        ![ForEach 작업](media/data-factory-command-activity/for-each-activity.png)

1.    캔버스에서 ForEach 활동을 선택합니다. 아래 설정 탭에서 다음을 **수행합니다.**
    * 조회 결과의 순차처리를 위해 **순차** 확인란을 선택하거나 선택하지 않은 상태로 두면 병렬 처리가 생성됩니다.
    * **일괄 처리 수를 설정합니다.**
    * **항목에서**출력 * @activity값('Lookup1')에* 대한 다음 참조를 제공합니다.

       ![ForEach 활동 설정](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>ForEach 활동 내에서 Azure 데이터 탐색기 명령 작업 만들기

1. 캔버스에서 ForEach 활동을 두 번 클릭하여 새 캔버스에서 열어 ForEach 내의 활동을 지정합니다.
1. **활동** 창에서 Azure **데이터 탐색기**에서 **Azure 데이터 탐색기 명령** 활동을 선택하고 캔버스에 끌어 놓습니다.

    ![Azure 데이터 탐색기 명령 작업](media/data-factory-command-activity/adx-command-activity.png)

1.    **연결** 탭에서 이전에 만든 것과 동일한 연결된 서비스를 선택합니다.

    ![azure 데이터 탐색기 명령 작업 연결 탭](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. **명령** 탭에서 다음 명령을 제공합니다.

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **명령은** Azure Data Explorer에 지정된 쿼리 결과를 압축된 형식으로 Blob 저장소로 내보내도록 지시합니다. 비동기 수정자를 사용하여 비동기적으로 실행됩니다.
    쿼리는 조회 활동 결과에서 각 행의 데이터베이스 열을 해결합니다. **명령 시간 정은** 변경 되지 않은 상태로 유지 될 수 있습니다.

    ![명령 활동](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > 명령 활동에는 다음과 같은 제한이 있습니다.
    > * 크기 제한: 1 MB 응답 크기
    > * 시간 제한: 20분(기본), 1시간(최대)
    > * 필요한 경우 [AdminThenQuery를](kusto/management/index.md#combining-queries-and-control-commands)사용하여 결과에 쿼리를 추가할 수 있으므로 결과 크기/시간을 줄일 수 있습니다.

1.    이제 파이프라인이 준비되었습니다. 파이프라인 이름을 클릭하여 기본 파이프라인 보기로 돌아갈 수 있습니다.

    ![Azure 데이터 탐색기 명령 파이프라인](media/data-factory-command-activity/adx-command-pipeline.png)

1. 파이프라인을 게시하기 전에 **디버그를** 선택합니다. **출력** 탭에서 파이프라인 진행률을 모니터링할 수 있습니다.

    ![azure 데이터 탐색기 명령 작업 출력](media/data-factory-command-activity/command-activity-output.png)

1. 모두 **게시한** 다음 **트리거를 추가하여** 파이프라인을 실행할 수 있습니다. 

## <a name="control-command-outputs"></a>제어 명령 출력

명령 활동 출력의 구조는 아래에 자세히 설명되어 있습니다. 이 출력은 파이프라인의 다음 활동에서 사용할 수 있습니다.

### <a name="returned-value-of-a-non-async-control-command"></a>비동기 제어 명령의 반환된 값

비동기 제어 명령에서 반환된 값의 구조는 조회 작업 결과의 구조와 유사합니다. 필드는 `count` 반환되는 레코드 수를 나타냅니다. 고정 배열 `value` 필드에는 레코드 목록이 포함되어 있습니다. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>비동기 제어 명령의 반환된 값

비동기 제어 명령에서 활동은 비동기 작업이 완료될 때까지 또는 시간 초과될 때까지 백그라운드에서 작업 테이블을 폴링을 합니다. 따라서 반환 된 값은 `.show operations OperationId` **주어진된 OperationId** 속성에 대 한 결과 포함 됩니다. **상태** 및 **상태** 속성의 값을 확인하여 작업이 성공적으로 완료되었는지 확인합니다.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>다음 단계

* Azure 데이터 [팩터리 를 사용하여 Azure 데이터 탐색기로 데이터를 복사하는](data-factory-load-data.md)방법에 대해 알아봅니다.
* [데이터베이스에서 Azure 데이터 탐색기로의 대량 복사본에 Azure Data Factory 템플릿을](data-factory-template.md)사용하는 방법에 대해 알아봅니다.
