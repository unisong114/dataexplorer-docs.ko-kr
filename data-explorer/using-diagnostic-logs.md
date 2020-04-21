---
title: 진단 로그를 사용하여 Azure Data Explorer 수집 작업 모니터링
description: Azure Data 탐색기의 진단 로그를 설정하여 수집 작업을 모니터링하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: c4c7114dd53b8f77150f88cf3da5d26ca73bb93b
ms.sourcegitcommit: 2baa6a1c25ba25ef02a53b4f8d023ea821bd04ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81527591"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs"></a>진단 로그를 사용하여 Azure Data Explorer 수집 작업 모니터링

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음 데이터베이스에 대한 쿼리를 실행할 수 있도록 데이터를 데이터베이스의 테이블에 수집(로드)합니다. [Azure Monitor 진단 로그는](/azure/azure-monitor/platform/diagnostic-logs-overview) Azure 리소스의 작업에 대한 데이터를 제공합니다. Azure Data Explorer는 진단 로그를 사용하여 수집 성공 및 실패에 대한 통찰력을 제공합니다. 작업 로그를 Azure 저장소, 이벤트 허브 또는 로그 분석으로 내보내 수집 상태를 모니터링할 수 있습니다. 추가 분석을 위해 Azure 저장소 및 Azure 이벤트 허브의 로그를 Azure Data Explorer 클러스터의 테이블로 라우팅할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 [무료 Azure 계정을](https://azure.microsoft.com/free/)만듭니다.
* 클러스터 [및 데이터베이스](create-cluster-database-portal.md)만들기 .

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대한 진단 로그 설정

진단 로그를 사용하여 다음 로그 데이터의 컬렉션을 구성할 수 있습니다.
* 성공적인 수집 작업: 이러한 로그에는 성공적으로 완료된 수집 작업에 대한 정보가 있습니다.
* 실패한 수집 작업: 이러한 로그에는 오류 세부 정보를 포함하여 실패한 수집 작업에 대한 자세한 정보가 있습니다. 

그런 다음 데이터는 저장소 계정에 보관되거나 이벤트 허브로 스트리밍되거나 사양에 따라 Log Analytics로 전송됩니다.

### <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 사용하려면 다음 단계를 수행합니다.

1. Azure [포털에서](https://portal.azure.com)모니터링할 Azure 데이터 탐색기 클러스터 리소스를 선택합니다.
1. **모니터링** 아래에서 **진단 설정**을 선택합니다.
  
    ![진단 로그 추가](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **진단 설정 추가를**선택합니다.
1. 진단 설정 창에서 **다음을 수행합니다.**
 
    ![진단 설정 구성](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 진단 설정의 **이름을** 선택합니다.
    1. 저장소 계정, 이벤트 허브 또는 로그 분석과 같은 하나 이상의 대상을 선택합니다.
    1. 수집할 `SucceededIngestion` `FailedIngestion`로그를 선택합니다.
    1. 수집할 [메트릭을 선택합니다(선택](using-metrics.md#supported-azure-data-explorer-metrics) 사항).  
    1. 새 진단 로그 설정 및 메트릭을 저장하려면 **저장을** 선택합니다.

몇 분 안에 새 설정이 설정됩니다. 그런 다음 로그가 구성된 보관 대상(저장소 계정, 이벤트 허브 또는 로그 분석)에 나타납니다. 

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 [Azure Monitor 진단 로그는 공통 최상위 스키마를 공유합니다.](/azure/azure-monitor/platform/diagnostic-logs-schema) Azure Data 탐색기는 자체 이벤트에 대한 고유한 속성을 가있습니다. 모든 로그는 JSON 형식으로 저장됩니다.

### <a name="ingestion-logs-schema"></a>인기 로그 스키마

Log JSON 문자열에는 다음 표에 나열된 요소가 포함됩니다.

|속성               |Description
|---                |---
|time               |보고서 시간
|resourceId         |Azure Resource Manager 리소스 ID
|operationName      |작업 이름: '마이크로 소프트. KUSTO/클러스터/인제스트/액션'
|operationVersion   |스키마 버전: '1.0' 
|category           |작업의 범주입니다. `SucceededIngestion` 또는 `FailedIngestion`입니다. 속성은 [성공 작업](#successful-ingestion-operation-log) 또는 [실패한 작업에](#failed-ingestion-operation-log)대해 다릅니다.
|properties         |작업에 대한 자세한 정보입니다.

#### <a name="successful-ingestion-operation-log"></a>성공적인 인고 작업 로그

**예제:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**성공적인 작업 진단 로그의 속성**

|속성               |Description
|---                |---
|성공        |섭취 완료 시간
|operationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름
|테이블              |대상 테이블의 이름
|인제션소스Id  |수집 데이터 원본의 ID
|인시션소스패스|수집 데이터 원본 또는 Blob URI의 경로
|루트활동Id     |활동 ID

#### <a name="failed-ingestion-operation-log"></a>실패한 인스레이션 작업 로그

**예제:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**실패한 작업 진단 로그의 속성**

|속성               |Description
|---                |---
|실패           |섭취 완료 시간
|operationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름
|테이블              |대상 테이블의 이름
|인제션소스Id  |수집 데이터 원본의 ID
|인시션소스패스|수집 데이터 원본 또는 Blob URI의 경로
|루트활동Id     |활동 ID
|자세히            |오류 및 오류 메시지에 대한 자세한 설명
|errorCode          |오류 코드 
|실패 상태      |`Permanent` 또는 `Transient`입니다. 일시적인 실패를 다시 시도하면 성공할 수 있습니다.
|시작From업데이트정책|업데이트 정책에서 오류가 발생한 경우 true
|재사용        |재시도가 성공할 수 있는 경우 True

## <a name="next-steps"></a>다음 단계

* [자습서: Azure 데이터 탐색기에서 데이터 수집 및 쿼리 모니터링](ingest-data-no-code.md)
* [메트릭을 사용하여 클러스터 상태 모니터링](using-metrics.md)

