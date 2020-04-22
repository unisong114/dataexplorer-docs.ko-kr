---
title: Azure 데이터 팩터리와 Azure 데이터 탐색기 통합
description: 이 항목에서는 Azure 데이터 탐색기를 Azure 데이터 팩터리와 통합하여 복사, 조회 및 명령 활동을 사용합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 5b8e9844894df9c49c1abd703ebc5a14b4c7050c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497968"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure 데이터 탐색기와 Azure 데이터 팩터리 통합

[Azure 데이터](/azure/data-factory/) 팩터리(ADF)는 클라우드 기반 데이터 통합 서비스로, 다양한 데이터 저장소를 통합하고 데이터에 대한 활동을 수행할 수 있습니다. ADF를 사용하면 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위한 데이터 기반 워크플로를 만들 수 있습니다. Azure 데이터 탐색기는 Azure 데이터 팩터리에서 [지원되는 데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 중 하나입니다. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 데이터 탐색기용 Azure 데이터 팩터리 활동

Azure 데이터 탐색기 사용자는 Azure 데이터 팩터리와의 다양한 통합을 사용할 수 있습니다.

### <a name="copy-activity"></a>복사 활동
 
Azure 데이터 팩터리 복사 활동은 데이터 저장소 간에 데이터를 전송하는 데 사용됩니다. Azure Data Explorer는 Azure Data Explorer에서 지원되는 데이터 저장소로 데이터가 복사되고 지원되는 데이터 저장소에서 Azure Data 탐색기로 데이터가 복사되는 싱크로 지원됩니다. 자세한 내용은 [Azure 데이터 팩터리를 사용하여 Azure 데이터 탐색기에서 또는 Azure 데이터 탐색기에서 데이터를 복사하는 것을 참조하세요.](/azure/data-factory/connector-azure-data-explorer) 자세한 내용은 Azure 데이터 [팩터리에서 Azure 데이터 탐색기로 의 로드 데이터를](data-factory-load-data.md)참조하십시오.
Azure 데이터 탐색기는 Azure IR(통합 런타임)에서 지원되며, Azure 내에서 데이터를 복사할 때 사용되며, 온-프레미스에 있는 데이터 저장소또는 Azure 가상 네트워크와 같은 액세스 제어가 있는 네트워크에서 데이터를 복사할 때 사용되는 자체 호스팅 IR이 사용됩니다. 자세한 내용은 [사용할 IR을](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use) 참조하십시오.
 
> [!TIP]
> 복사 활동을 사용하고 연결된 **서비스** 또는 **데이터 집합을**만들 때 이전 데이터 저장소 **Kusto가**아닌 데이터 저장소 **Azure 데이터 탐색기(Kusto)를** 선택합니다.  

### <a name="lookup-activity"></a>조회 활동
 
조회 활동은 Azure 데이터 탐색기에서 쿼리를 실행하는 데 사용됩니다. 쿼리 결과는 조회 활동의 출력으로 반환되며 [ADF 조회 설명서에](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)설명된 대로 파이프라인의 다음 활동에 사용할 수 있습니다.  
응답 크기 제한 5,000개 및 2MB 외에도 활동에는 쿼리 시간 제한 시간이 1시간입니다.

### <a name="command-activity"></a>명령 활동

명령 작업을 사용하면 Azure Data 탐색기 컨트롤 명령을 실행할 수 [있습니다.](kusto/concepts/index.md#control-commands) 쿼리와 달리 제어 명령은 잠재적으로 데이터 또는 메타데이터를 수정할 수 있습니다. 일부 제어 명령은 `.ingest`Azure Data Explorer에 데이터를 수집하거나 또는 Azure `.set-or-append`Data Explorer의 데이터를 와 같은 `.export`명령을 사용하여 외부 데이터 저장소로 복사하는 것을 목표로 합니다.
명령 활동에 대한 자세한 내용은 Azure [Data Factory 명령 활동 사용을 사용하여 Azure Data 탐색기 제어 명령을 실행합니다.](data-factory-command-activity.md)  컨트롤 명령을 사용하여 데이터를 복사하는 것은 때때로 복사 활동보다 빠르고 저렴할 수 있습니다. 명령 활동과 복사 활동을 사용할 시기를 결정하려면 [데이터를 복사할 때 복사 및 명령 활동 중 선택](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)을 참조하십시오.

### <a name="copy-in-bulk-from-a-database-template"></a>데이터베이스 템플릿에서 대량 복사

[Azure Data Factory 템플릿을 사용하여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사하는](data-factory-template.md) 것은 미리 정의된 Azure Data Factory 파이프라인입니다. 템플릿은 더 빠른 데이터 복사를 위해 데이터베이스 또는 테이블당 많은 파이프라인을 만드는 데 사용됩니다. 

### <a name="mapping-data-flows"></a>데이터 흐름 매핑 

[Azure Data Factory 매핑 데이터 흐름은](/azure/data-factory/concepts-data-flow-overview) 데이터 엔지니어가 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 시각적으로 설계된 데이터 변환입니다. Azure 데이터 탐색기로 데이터 흐름을 만들고 데이터를 수집하려면 다음 방법을 사용합니다.

1. 매핑 [데이터 흐름을](/azure/data-factory/data-flow-create)만듭니다.
1. [Azure Blob로 데이터를 내보냅니다.](/azure/data-factory/data-flow-sink) 
1. Azure 데이터 탐색기로 데이터를 수집하기 위해 [이벤트 그리드](/azure/data-explorer/ingest-data-event-grid) 또는 [ADF 복사 활동을](/azure/data-explorer/data-factory-load-data) 정의합니다.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>복사 및 Azure 데이터 탐색기 명령 활동 복사 할 때 데이터 복사 중에서 선택 

이 섹션에서는 데이터 복사 요구에 맞는 올바른 활동을 선택하는 데 도움이 됩니다.

Azure 데이터 탐색기에서 또는 Azure 데이터 탐색기로 데이터를 복사할 때 Azure 데이터 팩터리에서 두 가지 사용 가능한 옵션이 있습니다.
* 활동을 복사합니다.
* Azure 데이터 탐색기에서 데이터를 전송하는 제어 명령 중 하나를 실행하는 Azure 데이터 탐색기 명령 작업입니다. 

### <a name="copy-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 데이터 복사
  
복사 활동 또는 명령을 사용하여 Azure 데이터 [`.export`](kusto/management/data-export/index.md) 탐색기의 데이터를 복사할 수 있습니다. `.export` 명령은 쿼리를 실행 한 다음 쿼리의 결과를 내보냅니다. 

Azure 데이터 탐색기에서 데이터를 복사하기 `.export` 위한 복사 활동 및 명령의 비교는 다음 표를 참조하십시오.

| | 복사 활동 | 명령 내보내기 |
|---|---|---|
| **흐름 설명** | ADF는 Kusto에서 쿼리를 실행하고 결과를 처리한 다음 대상 데이터 저장소로 보냅니다. <br>**(ADX > ADF > 싱크 데이터 저장소)** | ADF는 `.export` 명령을 실행하는 Azure Data Explorer에 제어 명령을 보내고 데이터를 대상 데이터 저장소로 직접 보냅니다. <br>**(ADX > 싱크 데이터 저장소)** |
| **지원되는 대상 데이터 저장소** | [지원되는](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 다양한 데이터 저장소 | ADLSv2, Azure Blob, SQL 데이터베이스 |
| **성능** | 중앙 집중식 | <ul><li>분산(기본값) 여러 노드에서 동시에 데이터 내보내기</li><li>더 빠르고 COGS 효율적입니다.</li></ul> |
| **서버 제한** | [쿼리 제한을](kusto/concepts/querylimits.md) 확장/비활성화할 수 있습니다. 기본적으로 ADF 쿼리에는 다음이 포함됩니다. <ul><li>500,000개의 레코드 또는 64MB의 크기 제한입니다.</li><li>시간 제한은 10분입니다.</li><li>`noTruncation`false로 설정합니다.</li></ul> | 기본적으로 쿼리 제한을 확장하거나 사용하지 않도록 설정합니다. <ul><li>크기 제한은 사용할 수 없습니다.</li><li>서버 시간 시간이 1시간으로 연장됩니다.</li><li>`MaxMemoryConsumptionPerIterator`최대값(5GB, 총물리메모리/2)으로 `MaxMemoryConsumptionPerQueryPerNode` 확장됩니다.</li></ul>

> [!TIP]
> 복사 대상이 `.export` 명령에서 지원하는 데이터 저장소 중 하나이고 복사 활동 기능이 필요에 중요하지 않은 경우 `.export` 명령을 선택합니다.

### <a name="copying-data-to-azure-data-explorer"></a>Azure 데이터 탐색기로 데이터 복사

쿼리에서 수집 (,`.set-or-append` `.set-or-replace` `.set` `.replace)`및 [저장소에서](kusto/management/data-ingestion/ingest-from-query.md) [수집()과](kusto/management/data-ingestion/ingest-from-storage.md) `.ingest`같은 복사 활동 또는 수집 명령을 사용하여 데이터를 Azure Data Explorer에 복사할 수 있습니다. 

복사 활동 및 Azure 데이터 탐색기로 데이터를 복사하기 위한 수집 명령을 비교하려면 다음 표를 참조하십시오.

| | 복사 활동 | 쿼리에서 수집<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 스토리지에서 수집 <br> `.ingest` |
|---|---|---|---|
| **흐름 설명** | ADF는 원본 데이터 저장소에서 데이터를 가져옵니다. 그런 다음 ADF는 데이터를 Azure Blob에 업로드하고 청크로 분할한 다음 Blob을 다운로드하여 ADX 테이블로 수집합니다. <br> **(ADF > Azure blob> ADX)> 원본 데이터 저장소)** | 이러한 명령은 쿼리 또는 `.show` 명령을 실행하고 쿼리 결과를 테이블(ADX >**ADX)으로**인제할 수 있습니다. | 이 명령은 하나 이상의 클라우드 저장소 아티팩트에서 데이터를 "끌어당기"하여 데이터를 테이블로 수집합니다. |
| **지원되는 원본 데이터 저장소** |  [다양한 옵션](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL(sql_request 플러그인 사용), 코스모스(cosmosdb_sql_request 플러그인 사용) 및 HTTP 또는 Python API를 제공하는 기타 데이터 저장소입니다. | 파일 시스템, Azure Blob 저장소, ADLS Gen 1, ADLS Gen 2 |
| **성능** | 인스티온은 큐에 대기 및 관리되어 소규모 의 인스티온을 보장하고 부하 분산, 재시도 및 오류 처리를 제공하여 고가용성을 보장합니다. | <ul><li>이러한 명령은 대용량 데이터 가져오기를 위해 설계되지 않았습니다.</li><li>예상대로 저렴하고 작동합니다. 그러나 프로덕션 시나리오와 트래픽 속도 및 데이터 크기가 큰 경우 복사 작업을 사용합니다.</li></ul> |
| **서버 제한** | <ul><li>크기 제한 없음.</li><li>최대 제한 시간: 섭취한 Blob당 1시간입니다. |<ul><li>쿼리 부분에는 크기 제한이 있으며, 을 지정하여 `noTruncation=true`건너뛸 수 있습니다.</li><li>최대 제한 시간: 1시간.</li></ul> | <ul><li>크기 제한 없음.</li><li>최대 제한 시간: 1시간.</li></ul>|

> [!TIP]
> * ADF에서 Azure 데이터 탐색기로 데이터를 `ingest from query` 복사할 때 명령을 사용합니다.  
> * 대용량 데이터 세트(>1GB)의 경우 복사 활동을 사용합니다.  

## <a name="required-permissions"></a>필요한 사용 권한

다음 표에는 Azure 데이터 팩터리와의 통합에서 다양한 단계에 필요한 사용 권한이 나열되어 있습니다.

| 단계 | 작업(Operation) | 최소 사용 권한 수준 | 메모 |
|---|---|---|---|
| **연결된 서비스 만들기** | 데이터베이스 탐색 | *데이터베이스 뷰어* <br>ADF를 사용하는 로그인한 사용자는 데이터베이스 메타데이터를 읽을 수 있는 권한이 있어야 합니다. | 사용자는 데이터베이스 이름을 수동으로 제공할 수 있습니다. |
| | 연결을 테스트 | *데이터베이스 모니터* 또는 *테이블 인제스트레이터* <br>서비스 주체는 데이터베이스 수준 `.show` 명령 또는 테이블 수준 구성을 실행할 수 있는 권한이 있어야 합니다. | <ul><li>TestConnection은 데이터베이스가 아닌 클러스터에 대한 연결을 확인합니다. 데이터베이스가 없는 경우에도 성공할 수 있습니다.</li><li>테이블 관리자 권한으로는 충분하지 않습니다.</li></ul>|
| **데이터 집합 만들기** | 테이블 탐색 | *데이터베이스 모니터* <br>ADF를 사용하여 로그인한 사용자는 데이터베이스 수준 `.show` 명령을 실행할 권한이 있어야 합니다. | 사용자는 테이블 이름을 수동으로 제공할 수 있습니다.|
| **데이터 집합** 또는 **복사 활동** 만들기 | 데이터 미리 보기 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타데이터를 읽을 수 있는 권한이 있어야 합니다. | | 
|   | 스키마 가져오기 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타데이터를 읽을 수 있는 권한이 있어야 합니다. | ADX가 테이블 형식 간 복사본의 소스인 경우 ADF는 사용자가 스키마를 명시적으로 가져오지 않은 경우에도 스키마를 자동으로 가져옵니다. |
| **싱크대로 ADX** | 이름별 열 매핑 만들기 | *데이터베이스 모니터* <br>서비스 주체는 데이터베이스 수준 `.show` 명령을 실행할 수 있는 권한이 있어야 합니다. | <ul><li>모든 필수 작업은 *테이블 인제스트레이터에서*작동합니다.</li><li> 일부 선택적 작업이 실패할 수 있습니다.</li></ul> |
|   | <ul><li>테이블에 CSV 매핑 만들기</li><li>매핑 삭제</li></ul>| *테이블 인제스트또는* *데이터베이스 관리자* <br>서비스 주체는 테이블을 변경할 수 있는 권한이 있어야 합니다. | |
|   | 데이터 수집 | *테이블 인제스트또는* *데이터베이스 관리자* <br>서비스 주체는 테이블을 변경할 수 있는 권한이 있어야 합니다. | | 
| **소스로 ADX** | 쿼리 실행 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타데이터를 읽을 수 있는 권한이 있어야 합니다. | |
| **쿠스토 명령** | | 각 명령의 권한 수준에 따라. |

## <a name="performance"></a>성능 

Azure Data Explorer가 원본인 경우 쿼리가 포함된 조회, 복사 또는 명령 작업을 사용하는 경우 성능 정보에 대한 [쿼리 모범 사례](kusto/query/best-practices.md) 및 복사 활동에 대한 [ADF 설명서를](/azure/data-factory/copy-activity-performance)참조하십시오.
  
이 섹션에서는 Azure 데이터 탐색기가 싱크인 복사 활동의 사용을 다룹니다. Azure 데이터 탐색기 싱크의 예상 처리량은 11~13MBps입니다. 다음 표에서는 Azure 데이터 탐색기 싱크의 성능에 영향을 미치는 매개 변수를 자세히 설명합니다.

| 매개 변수 | 메모 |
|---|---|
| **구성 요소 지리적 근접성** | 모든 구성요소를 동일한 영역에 배치합니다.<ul><li>소스 및 싱크 데이터 저장소.</li><li>ADF 통합 런타임.</li><li>ADX 클러스터.</li></ul>적어도 통합 런타임이 ADX 클러스터와 동일한 지역에 있는지 확인합니다. |
| **DIUs 수** | ADF에서 사용하는 4개의 DIUs마다 1VM. <br>DIUs를 늘리면 소스가 여러 파일이 있는 파일 기반 저장소인 경우에만 도움이 됩니다. 그러면 각 VM이 다른 파일을 병렬로 처리합니다. 따라서 하나의 대용량 파일을 복사하면 여러 개의 작은 파일을 복사하는 것보다 대기 시간이 더 높습니다.|
|**ADX 클러스터의 양 및 SKU** | 많은 수의 ADX 노드가 처리 시간을 향상시킵니다.|
| **병렬 처리** |    데이터베이스에서 매우 많은 양의 데이터를 복사하려면 데이터를 분할한 다음 각 파티션을 병렬로 복사하는 ForEach 루프를 사용하거나 [데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사본을](data-factory-template.md)사용합니다. 참고: **복사** > 활동의**병렬 처리 정도설정은** ADX와 관련이 없습니다. |
| **데이터 처리 복잡성** | 대기 시간은 소스 파일 형식, 열 매핑 및 압축에 따라 다릅니다.|
| **통합 런타임을 실행하는 VM** | <ul><li>Azure 복사본의 경우 ADF VM 및 컴퓨터 SCO를 변경할 수 없습니다.</li><li> 온프레마에서 Azure 복사본에 대한 경우 자체 호스팅 IR을 호스팅하는 VM이 충분히 강력한지 확인합니다.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>팁과 일반적인 함정

### <a name="monitor-activity-progress"></a>활동 진행 상황 모니터링

* 활동 진행률을 모니터링할 때 *데이터 읽기는* 이진 파일 크기에 따라 계산되고, 데이터가 직렬화되고 압축 해제된 후 *기록된 데이터는* 메모리 내 크기에 따라 계산되므로 *Data 기록* 속성이 Data *read* 속성보다 훨씬 클 수 있습니다.

* 활동 진행률을 모니터링할 때 데이터가 Azure Data Explorer 싱크에 기록된 것을 볼 수 있습니다. Azure 데이터 탐색기 테이블을 쿼리할 때 데이터가 도착하지 않은 것을 볼 수 있습니다. Azure 데이터 탐색기로 복사할 때 두 단계가 있기 때문입니다. 
    * 첫 번째 단계는 원본 데이터를 읽고 900MB 청크로 분할한 다음 각 청크를 Azure Blob에 업로드합니다. 첫 번째 단계는 ADF 활동 진행률 보기에서 볼 수 있습니다. 
    * 두 번째 단계는 모든 데이터가 Azure Blob에 업로드되면 시작됩니다. Azure Data Explorer 엔진 노드는 Blob을 다운로드하고 데이터를 싱크 테이블에 수집합니다. 그러면 데이터는 Azure 데이터 탐색기 테이블에 표시됩니다.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>부적절한 이스케이프로 인해 CSV 파일을 섭취하지 못했습니다.

Azure 데이터 탐색기는 CSV 파일이 [RFC 4180에](https://www.ietf.org/rfc/rfc4180.txt)정렬될 것으로 예상합니다.
그것은 기대 :
* 이스케이프가 필요한 문자(예: "및 새 줄)가 포함된 필드는 공백 없이 **"문자로** 시작하고 끝나야 합니다. 필드 *내의* 모든 **"** 문자는 이중 **"** 문자 **(")를**사용하여 이스케이프됩니다. 예를 들어 _"Hello, "World""는_ _"Hello"_ 콘텐츠가 있는 단일 열 또는 필드가 있는 단일 레코드가 있는 유효한 CSV 파일입니다.
* 파일의 모든 레코드에는 동일한 수의 열과 필드가 있어야 합니다.

Azure 데이터 팩터리는 백슬래시(이스케이프) 문자를 허용합니다. Azure Data Factory를 사용하여 백슬래시 문자가 있는 CSV 파일을 생성하는 경우 Azure 데이터 탐색기로 파일을 수집하지 못합니다.

#### <a name="example"></a>예제

다음 텍스트 값: 안녕하세요, "세계"<br/>
ABC 데프<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

다음과 같이 적절한 CSV 파일에 표시되어야 합니다.<br/>
"ABC DEF"<br/>
"""ABC DEF"<br/>
"""ABC\D"""EF"<br/>

기본 이스케이프 문자(백슬래시)를 사용하면 다음 CSV가 Azure 데이터 탐색기에서 작동하지 않습니다. \"\"<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>중첩된 JSON 개체

JSON 파일을 Azure 데이터 탐색기로 복사할 때 다음 을 유의하십시오.
* 배열은 지원되지 않습니다.
* JSON 구조에 개체 데이터 형식이 포함된 경우 Azure Data Factory는 개체의 자식 항목을 병합하고 각 자식 항목을 Azure Data 탐색기 테이블의 다른 열에 매핑하려고 시도합니다. 전체 개체 항목을 Azure 데이터 탐색기의 단일 열에 매핑하려면 다음을 수행합니다.
    * 전체 JSON 행을 Azure 데이터 탐색기의 단일 동적 열로 인더스트합니다.
    * Azure 데이터 팩터리의 JSON 편집기에서 파이프라인 정의를 수동으로 편집합니다. **매핑에서**
       * 각 자식 항목에 대해 생성된 여러 매핑을 제거하고 객체 유형을 테이블 열에 매핑하는 단일 매핑을 추가합니다.
       * 닫는 사각형 대괄호 뒤에 쉼표를 추가한 다음 다음을 수행합니다.<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Azure 데이터 탐색기로 복사할 때 추가속성 지정

> [!NOTE]
> 이 기능은 현재 JSON 페이로드를 수동으로 편집하여 사용할 수 있습니다. 

다음과 같이 복사 활동의 "싱크" 섹션 아래에 단일 행을 추가합니다.

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

값을 이스케이프하는 것은 까다로울 수 있습니다. 다음 코드 조각을 참조로 사용합니다.

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

인쇄된 값:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 팩터리 를 사용하여 Azure 데이터 탐색기로 데이터를 복사하는](data-factory-load-data.md)방법에 대해 알아봅니다.
* [데이터베이스에서 Azure 데이터 탐색기로의 대량 복사본에 Azure Data Factory 템플릿을](data-factory-template.md)사용하는 방법에 대해 알아봅니다.
* Azure Data Factory 명령 작업을 사용하여 [Azure 데이터 탐색기 제어 명령을 실행하는](data-factory-command-activity.md)방법에 대해 알아봅니다.
* 데이터 [쿼리에 대한 Azure 데이터 탐색기 쿼리에](/azure/data-explorer/web-query-data) 대해 자세히 알아보세요.



