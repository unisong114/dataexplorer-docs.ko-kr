---
title: 메트릭을 사용 하 여 Azure 데이터 탐색기 성능, 상태 & 사용량 모니터링
description: Azure 데이터 탐색기 메트릭을 사용 하 여 클러스터의 성능, 상태 및 사용량을 모니터링 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/19/2020
ms.custom: contperfq1
ms.openlocfilehash: 153b4265aade03e4059db0b38362d217cdad90df
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748419"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>메트릭을 사용 하 여 Azure 데이터 탐색기 성능, 상태 및 사용 현황 모니터링

Azure 데이터 탐색기 메트릭은 Azure 데이터 탐색기 클러스터 리소스의 상태 및 성능에 대 한 주요 표시기를 제공 합니다. 이 문서에 자세히 설명 된 메트릭을 사용 하 여 특정 시나리오에서 Azure 데이터 탐색기 클러스터 사용량, 상태 및 성능을 독립 실행형 메트릭으로 모니터링할 수 있습니다. 또한 작업 [Azure 대시보드](/azure/azure-portal/azure-portal-dashboards) 및 [azure 경고](/azure/azure-monitor/platform/alerts-metric-overview)에 대 한 기준으로 메트릭을 사용할 수 있습니다.

Azure 메트릭 탐색기에 대 한 자세한 내용은 [메트릭 탐색기](/azure/azure-monitor/platform/metrics-getting-started)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 계정이 없는 경우 [무료 Azure 계정을](https://azure.microsoft.com/free/)만들 수 있습니다.
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="use-metrics-to-monitor-your-azure-data-explorer-resources"></a>메트릭을 사용 하 여 Azure 데이터 탐색기 리소스 모니터링

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure 데이터 탐색기 클러스터의 왼쪽 창에서 *메트릭* 을 검색 합니다.
1. 메트릭 **을 선택 하** 여 메트릭 창을 열고 클러스터에 대 한 분석을 시작 합니다.
    :::image type="content" source="media/using-metrics/select-metrics.gif" alt-text="Azure Portal에서 메트릭을 검색 하 고 선택 합니다.":::

## <a name="work-in-the-metrics-pane"></a>메트릭 창에서 작업

메트릭 창에서 추적할 특정 메트릭을 선택 하 고, 데이터를 집계 하는 방법을 선택 하 고, 대시보드에 표시할 메트릭 차트를 만듭니다.

**리소스** 및 **메트릭 네임 스페이스** 선택기는 Azure 데이터 탐색기 클러스터에 대해 미리 선택 됩니다. 다음 이미지의 숫자는 아래의 번호 매기기 목록에 해당 합니다. 메트릭 설정 및 보기에서 다양 한 옵션을 안내 합니다.

![메트릭 창](media/using-metrics/metrics-pane.png)

1. 메트릭 차트를 만들려면 **메트릭 이름 및 메트릭 별** 관련 **집계** 를 선택 합니다. 다른 메트릭에 대 한 자세한 내용은 [지원 되는 Azure 데이터 탐색기 메트릭](#supported-azure-data-explorer-metrics)을 참조 하세요.
1. **메트릭 추가** 를 선택 하 여 동일한 차트에 표시 되는 여러 메트릭을 표시 합니다.
1. **+ 새 차트** 를 선택 하 여 한 뷰에서 여러 차트를 볼 수 있습니다.
1. 시간 선택을 사용 하 여 시간 범위를 변경 합니다 (기본값: 지난 24 시간).
1. [ **추가 필터** 를 사용 **Apply splitting** 하 고](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) 차원이 있는 메트릭에 분할을 적용 합니다.
1. **대시보드에 고정** 을 선택 하 여 차트 구성을 다시 볼 수 있도록 대시보드에 추가 합니다.
1. 설정 된 조건을 사용 하 여 메트릭을 시각화 하도록 **새 경고 규칙** 을 설정 합니다. 새 경고 규칙에는 사용자 차트의 대상 리소스, 메트릭, 분할 및 필터 차원이 포함됩니다. [경고 규칙 만들기 창](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)에서 이러한 설정을 수정 합니다.

## <a name="supported-azure-data-explorer-metrics"></a>지원 되는 Azure 데이터 탐색기 메트릭

Azure 데이터 탐색기 메트릭은 수집, 쿼리 등의 특정 작업에 대 한 정보 뿐만 아니라 리소스의 전반적인 성능과 사용에 대 한 통찰력을 제공 합니다. 이 문서의 메트릭은 사용 유형별로 그룹화 되었습니다. 

메트릭 유형은 다음과 같습니다. 
* [클러스터 메트릭](#cluster-metrics) 
* [메트릭 내보내기](#export-metrics) 
* [수집 메트릭](#ingestion-metrics) 
* [스트리밍 수집 메트릭](#streaming-ingest-metrics)
* [쿼리 메트릭](#query-metrics) 
* [구체화 된 뷰 메트릭](#materialized-view-metrics)

Azure 데이터 탐색기에 대 한 Azure Monitor 메트릭의 사전순 목록은 [지원 되는 azure 데이터 탐색기 클러스터 메트릭](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)을 참조 하세요.

## <a name="cluster-metrics"></a>클러스터 메트릭

클러스터 메트릭은 클러스터의 일반 상태를 추적 합니다. 예를 들어 리소스와 수집 사용 및 응답성이 있습니다.

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
| 캐시 사용률 | 백분율 | 평균, 최대값, 최소값 | 클러스터에서 현재 사용 중인 할당된 캐시 리소스의 비율입니다. 캐시는 정의된 캐시 정책에 따라 사용자 활동에 할당된 SSD의 크기입니다. <br> <br> 80% 이하의 평균 캐시 사용률이 클러스터에 대해 유지 가능한 상태입니다. 평균 캐시 사용률이 80%를 초과 하면 클러스터는 <br> 저장소에 최적화 된 가격 책정 계층으로 [확장](manage-cluster-vertical-scaling.md) 됨 <br> 더 많은 인스턴스로 [확장 되었습니다](manage-cluster-horizontal-scaling.md) . 또는 캐시 정책을 조정합니다(캐시에서 일 수 줄임). 캐시 사용률이 100%를 초과하는 경우 캐싱할 데이터의 크기는 캐싱 정책에 따라 클러스터의 전체 캐시 크기보다 큽니다. | 없음 |
| CPU | 백분율 | 평균, 최대값, 최소값 | 클러스터의 머신에서 현재 사용 중인 할당된 컴퓨팅 리소스의 비율입니다. <br> <br> 평균 CPU의 80% 이하가 클러스터에 대해 유지됩니다. CPU의 최댓값은 100%이며, 이는 데이터를 처리할 추가 컴퓨팅 리소스가 없음을 의미합니다. <br> 클러스터가 제대로 수행 되지 않으면 CPU의 최대값을 확인 하 여 차단 된 특정 Cpu가 있는지 확인 합니다. | 없음 |
| 수집 사용률 | 백분율 | 평균, 최대값, 최소값 | 수집을 수행하기 위해 용량 정책에서 할당된 총 리소스로부터 데이터를 수집하는 데 사용되는 실제 리소스의 비율입니다. 기본 용량 정책은 동시 수집 작업 수 512개 또는 수집에 투자된 클러스터 리소스의 75%를 넘지 않습니다. <br> <br> 80% 이하의 평균 수집 사용률이 클러스터에 대해 유지 가능한 상태입니다. 수집 사용률의 최댓값은 100%이며, 이는 모든 클러스터 수집 기능이 사용되고 수집 큐가 발생할 수 있음을 의미합니다. | 없음 |
| 연결 유지 | 개수 | Avg | 클러스터의 응답성을 추적합니다. <br> <br> 완전히 반응하는 클러스터는 값 1을 반환하고 차단되거나 연결이 끊긴 클러스터는 0을 반환합니다. |
| 제한 된 명령의 총 수 | 개수 | Avg, Max, Min, Sum | 허용 되는 최대 동시 (병렬) 명령 수에 도달 했으므로 클러스터에서 제한 된 (거부 된) 명령 수입니다. | 없음 |
| 총 익스텐트 수 | 개수 | Avg, Max, Min, Sum | 클러스터에 있는 총 데이터 익스텐트 수입니다. <br> <br> 데이터 익스텐트 병합은 CPU 사용량이 많은 작업 이므로이 메트릭에 대 한 변경으로 클러스터의 대규모 데이터 구조 변경 및 높은 부하가 암시 될 수 있습니다. | 없음 |

## <a name="export-metrics"></a>메트릭 내보내기

내보내기 메트릭은 지연, 결과, 레코드 수 및 사용률 같은 내보내기 작업의 일반 상태와 성능을 추적 합니다.

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
내보낸 레코드의 연속 내보내기 수    | 개수 | 합계 | 모든 연속 내보내기 작업에서 내보낸 레코드 수입니다. | ContinuousExportName |
연속 내보내기 최대 지연 |    개수   | Max   | 클러스터의 연속 내보내기 작업에서 보고 한 지연 (분)입니다. | 없음 |
연속 내보내기 보류 중 수 | 개수 | Max   | 보류 중인 연속 내보내기 작업 수입니다. 이러한 작업은 실행할 준비가 되었지만 용량 부족으로 인해 큐에서 대기 하 고 있습니다. 
연속 내보내기 결과    | 개수 |   개수   | 각 연속 내보내기 실행의 실패/성공 결과입니다. | ContinuousExportName |
사용률 내보내기 |    백분율 | Max   | 클러스터의 총 내보내기 용량 (0에서 100 사이)에서 사용 되는 내보내기 용량입니다. | 없음 |

## <a name="ingestion-metrics"></a>수집 메트릭

수집 메트릭은 대기 시간, 결과 및 볼륨과 같은 수집 작업의 일반적인 상태와 성능을 추적 합니다.

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
| 일괄 처리 blob 수 | 개수 | 평균, 최대값, 최소값 | 수집을 위해 완료 된 일괄 처리의 데이터 원본 수입니다. | 데이터베이스 |
| 일괄 처리 기간 | 초 | 평균, 최대값, 최소값 | 수집 흐름에서 일괄 처리 단계의 기간  | 데이터베이스 |
| Batch 크기 | 바이트 | 평균, 최대값, 최소값 | 수집을 위해 집계 된 일괄 처리에서 압축 되지 않은 예상 데이터 크기입니다. | 데이터베이스 |
| 일괄 처리 | 개수 | 평균, 최대값, 최소값 | 수집을 위해 완료 된 일괄 처리 수입니다. `Batching Type`: 일괄 처리가 일괄 처리 시간, 데이터 크기 또는 파일 수 제한에 도달 했는지 여부를 일괄 처리 [정책](./kusto/management/batchingpolicy.md)으로 설정 합니다. | 데이터베이스, 일괄 처리 유형 |
| 검색 대기 시간 | 초 | 평균, 최대값, 최소값 | 데이터 연결을 통해 검색 될 때까지 데이터 큐에서 대기 하는 시간입니다. 이 시간은 **Azure 데이터 탐색기 총 수집 기간** 또는 수집 **대기 시간 (KustoEventAge)** 에 포함 되지 않습니다. | 데이터베이스, 테이블, 데이터 연결 형식, 데이터 연결 이름 |
| 이벤트 처리됨(Event/IoT Hubs의 경우) | 개수 | 최대, 최소, 합계 | Event hubs에서 읽어서 클러스터에서 처리 한 총 이벤트 수입니다. 이벤트는 거부 된 이벤트 및 클러스터 엔진에서 받은 이벤트로 분할 됩니다. | EventStatus |
| 수집 대기 시간 | 초 | 평균, 최대값, 최소값 | 클러스터에서 데이터를 수신한 시점부터 쿼리 준비가 될 때까지 수집된 데이터의 지연 시간입니다. 수집 대기 시간은 수집 시나리오에 따라 달라집니다. | 없음 |
| 수집 결과 | 개수 | 개수 | 실패 하 고 성공한 수집 작업의 총 수입니다. <br> <br> **분할 적용** 을 사용 하 여 성공 및 실패 결과 버킷을 만들고 크기 (**값**  >  **상태**)를 분석 합니다.| IngestionResultDetails |
| 수집 볼륨(MB) | 개수 | 최대값, 합계 | 압축 전 클러스터에 수집 된 데이터의 총 크기 (MB)입니다. | 데이터베이스 |
| 단계 대기 시간 | 초 | 평균, 최대값, 최소값 | 특정 구성 요소에서이 일괄 처리 데이터를 처리 하는 기간입니다. 데이터 일괄 처리의 모든 구성 요소에 대 한 총 단계 대기 시간은 수집 대기 시간과 같습니다. | 데이터베이스, 데이터 연결 형식, 데이터 연결 이름|

## <a name="streaming-ingest-metrics"></a>스트리밍 수집 메트릭

스트리밍 수집 메트릭은 스트리밍 수집 데이터 및 요청 빈도, 기간 및 결과를 추적 합니다.

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
스트리밍 수집 데이터 속도 |    개수   | RateRequestsPerSecond | 클러스터에 수집 된 데이터의 전체 볼륨입니다. | 없음 |
스트리밍 수집 지속 시간   | 밀리초  | 평균, 최대값, 최소값 | 모든 스트리밍 수집 요청의 총 기간입니다. | 없음 |
스트리밍 수집 요청 속도   | 개수 | Count, Avg, Max, Min, Sum | 총 스트리밍 수집 요청 수입니다. | 없음 |
스트리밍 수집 결과 | 개수 | Avg   | 결과 유형별 총 스트리밍 수집 요청 수입니다. | 결과 |

## <a name="query-metrics"></a>쿼리 메트릭

쿼리 성능 메트릭은 쿼리 기간과 동시 또는 제한 된 쿼리의 총 수를 추적 합니다.

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
| 쿼리 기간 | 밀리초 | Avg, Min, Max, Sum | 쿼리 결과가 수신 될 때까지 걸리는 총 시간입니다 (네트워크 대기 시간을 포함 하지 않음). | QueryStatus |
| 총 동시 쿼리 수 | 개수 | Avg, Max, Min, Sum | 클러스터에서 병렬로 실행 되는 쿼리 수입니다. 이 메트릭은 클러스터의 부하를 예측 하는 좋은 방법입니다. | 없음 |
| 제한 된 쿼리의 총 수 | 개수 | Avg, Max, Min, Sum | 클러스터에서 제한 된 (거부 된) 쿼리의 수입니다. 허용 되는 동시 쿼리 (병렬)의 최대 수는 동시 쿼리 정책에 정의 되어 있습니다. | 없음 |

## <a name="materialized-view-metrics"></a>구체화 된 뷰 메트릭

|**메트릭** | **단위** | **요약** | **메트릭 설명** | **차원** |
|---|---|---|---|---|
|MaterializedViewHealth                    | 1, 0    | Avg     |  뷰가 정상으로 간주 되 면 값은 1이 고, 그렇지 않으면 0입니다. | 데이터베이스, MaterializedViewName |
|MaterializedViewAgeMinutes                | 분 | Avg     | `age`뷰의는 현재 시간에서 뷰에서 처리 된 마지막 수집 시간을 뺀 값으로 정의 됩니다. 메트릭 값은 시간 (분)입니다. 값이 낮을수록 뷰가 "healthier"입니다. | 데이터베이스, MaterializedViewName |
|MaterializedViewResult                    | 1       | Avg     | 메트릭은 `Result` 마지막 구체화 주기의 결과를 나타내는 차원을 포함 합니다 (아래의 가능한 값 참조). 메트릭 값은 항상 1과 같습니다. | 데이터베이스, MaterializedViewName, 결과 |
|MaterializedViewRecordsInDelta            | 레코드 수 | Avg | 원본 테이블에서 현재 처리 되지 않은 부분에 있는 레코드 수입니다. 자세한 내용은 [구체화 된 뷰 작동 방식](./kusto/management/materialized-views/materialized-view-overview.md#how-materialized-views-work) 을 참조 하세요.| 데이터베이스, MaterializedViewName |
|MaterializedViewExtentsRebuild            | 익스텐트 수 | Avg | 구체화 주기에서 다시 작성 된 익스텐트 수입니다. | 데이터베이스, MaterializedViewName|
|MaterializedViewDataLoss                  | 1       | Max    | 처리 되지 않은 원본 데이터가 보존에 근접 하는 경우 메트릭이 발생 합니다. | Database, MaterializedViewName, Kind |

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Data Explorer에서 모니터링 데이터 수집 및 쿼리](ingest-data-no-code.md)
* [진단 로그를 사용하여 Azure Data Explorer 수집 작업 모니터링](using-diagnostic-logs.md)
* [빠른 시작: Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
