---
title: 원클릭 수집을 사용하여 Azure Data Explorer에 데이터 수집
description: 원클릭 수집을 사용하여 간단하게 Azure Data Explorer에 데이터를 수집(로드)하는 방법에 대한 개요입니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: fa18a89e44a09a877cedb41c6d6e77384c965801
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274605"
---
# <a name="what-is-one-click-ingestion"></a>원클릭 수집이란?

원클릭 수집을 사용하면 데이터 수집 프로세스를 쉽고 빠르고 직관적으로 수행할 수 있습니다. 원클릭 수집은 신속하게 램프 업하여 데이터 수집을 시작하고, 데이터베이스 테이블을 만들고, 구조를 매핑하도록 도와줍니다. 일회성 또는 지속적인 수집 프로세스로 여러 소스의 데이터를 여러 데이터 형식으로 선택할 수 있습니다.

다음 기능을 사용하면 원클릭 수집을 유용하게 활용할 수 있습니다.

* 수집 마법사가 안내하는 직관적인 환경
* 몇 분 내에 데이터 수집
* 로컬 파일, BLOB, 컨테이너 등 여러 종류의 소스에서 데이터 수집(최대 10,000개 BLOB)
* 다양한 [형식](#file-formats)으로 데이터 수집
* 새 테이블 또는 기존 테이블에 데이터 수집
* 테이블 매핑 및 스키마는 자동으로 제안되며 쉽게 변경 가능
* Event Grid를 사용하여 컨테이너에서 쉽고 빠르게 지속적으로 수집

원클릭 수집은 데이터를 처음으로 수집하거나 데이터의 스키마가 익숙하지 않은 경우에 특히 유용합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.
* [Azure Data Explorer 웹 UI](https://dataexplorer.azure.com/)에 로그인하여 [클러스터에 연결을 추가](web-query-data.md#add-clusters)합니다.

## <a name="access-the-one-click-wizard"></a>원클릭 마법사 액세스

원클릭 수집 마법사는 원클릭 수집 프로세스를 안내합니다.

* 클러스터의 **Azure Data Explorer 시작** 홈 화면에서 원클릭 수집 마법사에 액세스하려면 처음 두 단계([클러스터 만들기 및 데이터베이스 만들기](#prerequisites))를 완료한 다음, **새 데이터 수집**을 선택합니다.

    :::image type="content" source="media/ingest-data-one-click/welcome-ingestion.png" alt-text="Azure Data Explorer 시작에서 새 데이터 수집":::

* [Azure Data Explorer 웹 UI](https://dataexplorer.azure.com/)에서 마법사에 액세스하려면 Azure Data Explorer 웹 UI의 왼쪽 메뉴에서 **데이터베이스** 또는 **테이블**을 마우스 오른쪽 단추로 클릭하고 **새 데이터 수집(미리 보기)** 을 선택합니다.

    :::image type="content" source="media/ingest-data-one-click/one-click-ingestion-in-webui.png" alt-text="웹 UI에서 원클릭 수집 선택":::

* Azure Portal에서 마법사에 액세스하려면 왼쪽 메뉴에서 **쿼리**를 선택하고 **데이터베이스** 또는 **테이블**을 마우스 오른쪽 단추로 클릭한 다음, **새 데이터(미리 보기) 수집**을 선택합니다.

    :::image type="content" source="media/ingest-data-one-click/access-from-portal.png" alt-text="Azure Portal에서 원클릭 수집 마법사에 액세스":::

## <a name="one-click-ingestion-wizard"></a>원클릭 수집 마법사

> [!NOTE]
> 이 섹션에서는 마법사의 일반적인 내용을 설명합니다. 선택하는 옵션은 수집하는 데이터 형식, 데이터를 수집하는 데이터 원본 유형, 새 테이블 또는 기존 테이블 중 어디에 수집할 것인지에 따라 달라집니다.
>
> 샘플 시나리오는 다음을 참조하세요.
> * [컨테이너의 새 테이블에 CSV 형식으로](one-click-ingestion-new-table.md) 수집
> * [로컬 파일의 기존 테이블에 JSON 형식으로](one-click-ingestion-existing-table.md) 수집 

이 마법사는 다음 옵션을 안내합니다.
   * [기존 테이블](one-click-ingestion-existing-table.md)에 수집
   * [새 테이블](one-click-ingestion-new-table.md)에 수집
   * 다음에서 데이터를 수집합니다.
      * Blob Storage
      * [로컬 파일](one-click-ingestion-existing-table.md)
      * [컨테이너](one-click-ingestion-new-table.md)


### <a name="schema-mapping"></a>스키마 매핑

서비스에서 스키마 및 수집 속성을 자동으로 생성하며, 사용자가 변경할 수 있습니다. 사용자는 새 테이블과 기존 테이블 중 어디에 수집할 것인지에 따라 기존 매핑 구조를 사용할 수도 있고 새로 만들 수도 있습니다.

**스키마** 탭에서 다음 작업을 수행합니다.
   * 자동 생성된 압축 형식을 확인합니다.
   * [데이터 형식](#file-formats)을 선택합니다. 여러 형식을 사용하면 추가 변경 작업을 수행할 수 있습니다.

#### <a name="file-formats"></a>파일 형식

원클릭 수집은 다음 형식의 원본 데이터에서 새 테이블을 수집할 수 있습니다.
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

### <a name="editor-window"></a>편집기 창

**편집기** 창에서 데이터 테이블 열을 필요한 대로 조정할 수 있습니다. 

|테이블 유형입니다.  |사용 가능한 열 조정  |
|---------|---------|
|새로 만들기     | 새 열, 열 삭제, 오름차순 정렬, 내림차순 정렬  |
|Existing     | 새 열, 오름차순 정렬, 내림차순 정렬  |

>[!NOTE]
> 언제든지 **편집기** 창 위에 있는 [명령 편집기](one-click-ingestion-new-table.md#command-editor)를 열 수 있습니다. 명령 편집기에서는 사용자 입력으로부터 생성된 자동 명령을 살펴보고 복사할 수 있습니다.

### <a name="data-ingestion"></a>데이터 수집

스키마 매핑과 열 조작을 완료하면 수집 마법사가 데이터 수집 프로세스를 시작합니다. 

* **컨테이너가 이닌** 원본에서 데이터를 수집하는 경우 수집이 즉시 적용됩니다.

* 데이터 원본이 **컨테이너**인 경우:
    * Azure Data Explorer의 [일괄 처리 정책](kusto/management/batchingpolicy.md)은 데이터를 집계합니다. 
    * 수집 후에는 수집 보고서를 다운로드하여 해결된 각 BLOB의 성능을 검토할 수 있습니다. 
    * **지속적인 수집 만들기**를 선택하고 [Event Grid를 사용하여 지속적인 수집](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)을 설정할 수 있습니다.
 
### <a name="initial-data-exploration"></a>초기 데이터 탐색
   
수집 후에는 마법사에서 데이터의 초기 탐색을 위한 **[빠른 명령](one-click-ingestion-existing-table.md#explore-quick-queries-and-tools)** 옵션을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer에서 원클릭 수집을 사용하여 로컬 파일의 JSON 데이터를 기존 테이블에 수집](one-click-ingestion-existing-table.md)
* [Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md)
* [Azure Data Explorer 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용하여 Azure Data Explorer에 대한 쿼리 작성](write-queries.md)
