---
title: 스트리밍 수집을 사용하여 Azure 데이터 탐색기로 데이터 수집
description: 스트리밍 수집을 사용하여 Azure 데이터 탐색기로 데이터를 수집(로드)하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 219a9014b120e0df74f8d9d286253fa933c8f05a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499619"
---
# <a name="streaming-ingestion-preview"></a>스트리밍 섭취(미리 보기)

다양한 볼륨 데이터의 경우 수집 시간이 10초 미만인 짧은 대기 시간이 필요한 경우 스트리밍 수집을 사용합니다. 하나 이상의 데이터베이스에서 각 테이블의 데이터 스트림이 상대적으로 작지만 전체 데이터 수집 볼륨(초당 수천 개)이 많은 테이블의 운영 처리를 최적화하는 데 사용됩니다. 

데이터 양이 테이블당 초당 1MB 이상으로 증가하는 경우 스트리밍 수집 대신 대량 수집을 사용합니다. [데이터 수집 개요를](/azure/data-explorer/ingest-data-overview) 읽고 다양한 수집 방법에 대해 자세히 알아보십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [웹 UI에](https://dataexplorer.azure.com/)로그인합니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기

## <a name="enable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 사용 설정

> [!WARNING]
> 찜 섭취를 활성화하기 전에 [제한 사항을](#limitations) 검토하십시오.

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. **설정에서** **구성을 선택합니다.** 
1. **구성** 창에서 **스트리밍 생성을**활성화하려면 **켜기를** 선택합니다.
1. **저장**을 선택합니다.
 
    ![스트리밍 섭취](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 웹 [UI에서는](https://dataexplorer.azure.com/)스트리밍 데이터를 [수신할](kusto/management/streamingingestionpolicy.md) 테이블 또는 데이터베이스에서 스트리밍 수집 정책을 정의합니다. 

    > [!NOTE]
    > * 데이터베이스 수준에서 정책이 정의된 경우 데이터베이스의 모든 테이블이 스트리밍 사용으로 활성화됩니다.
    > * 적용된 정책은 새로 수집된 데이터만 참조할 수 있으며 데이터베이스의 다른 테이블은 참조할 수 없습니다.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>스트리밍 수집을 사용하여 클러스터에 데이터 수집

지원되는 스트리밍 인식 유형은 두 가지가 있습니다.


* 데이터 원본으로 사용되는 [**이벤트 허브**](/azure/data-explorer/ingest-data-event-hub)
* **사용자 지정을** 사용하려면 Azure Data Explorer 클라이언트 라이브러리 중 하나를 사용하는 응용 프로그램을 작성해야 합니다. 샘플 응용 프로그램에 대한 [스트리밍 사용 샘플을](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) 참조하십시오.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>적절한 스트리밍 사용 유형 선택

|   |이벤트 허브  |사용자 지정 섭취  |
|---------|---------|---------|
|수집 시작과 쿼리에 사용할 수 있는 데이터 사이의 데이터 지연   |    더 긴 지연     |   짧은 지연      |
|개발 오버헤드    |   빠르고 쉬운 설정, 개발 오버헤드 없음    |   응용 프로그램에서 오류를 처리하고 데이터 일관성을 보장하기 위한 높은 개발 오버헤드     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 사용 안 함

> [!WARNING]
> 스트리밍 을 사용하지 않도록 설정하는 데 몇 시간이 걸릴 수 있습니다.

1. 모든 관련 테이블 및 데이터베이스에서 [스트리밍 인식 정책을](kusto/management/streamingingestionpolicy.md) 삭제합니다. 스트리밍 수집 정책 제거는 초기 저장소에서 열 저장소의 영구 저장소(익스텐트 또는 샤드)로의 스트리밍 수집 데이터 이동을 트리거합니다. 데이터 이동은 초기 저장소의 데이터 양과 클러스터에서 CPU와 메모리를 사용하는 방법에 따라 몇 초에서 몇 시간 사이에 지속될 수 있습니다.
1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. **설정에서** **구성을 선택합니다.** 
1. **구성** 창에서 **끄기를** 선택하여 **스트리밍 생성을 비활성화합니다.**
1. **저장**을 선택합니다.

    ![스트리밍 섭취 끄기](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>제한 사항

* 스트리밍 수집은 [데이터베이스 커서](kusto/management/databasecursor.md) 또는 데이터 [매핑을](kusto/management/mappings.md)지원하지 않습니다. [미리 만들어진](kusto/management/create-ingestion-mapping-command.md) 데이터 매핑만 지원됩니다. 
* VM 및 클러스터 크기가 증가하여 스트리밍 소모 성능 및 용량 이 확장됩니다. 동시 섭취는 코어당 6개의 섭취로 제한됩니다. 예를 들어 D14 및 L16과 같은 16개의 코어 SCO의 경우 최대 지원 로드는 96개의 동시 통합입니다. D11과 같은 두 개의 핵심 SCO의 경우 최대 지원 로드는 12개의 동시 통합입니다.
* 수집 요청당 데이터 크기 제한은 4MB입니다.
* 테이블 생성 및 수정 및 구성 매핑과 같은 스키마 업데이트는 스트리밍 사용 서비스에 최대 5분이 걸릴 수 있습니다.
* 스트리밍을 통해 데이터가 수집되지 않더라도 클러스터에서 스트리밍 수집을 사용하도록 설정하면 클러스터 컴퓨터의 로컬 SSD 디스크의 일부를 사용하여 수집 데이터를 스트리밍하고 핫 캐시에 사용할 수 있는 저장소를 줄입니다.
* [익스텐트 태그는](kusto/management/extents-overview.md#extent-tagging) 스트리밍 수집 데이터에 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기의 쿼리 데이터](web-query-data.md)
