---
title: Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집
description: 원클릭 수집을 사용하여 간단하게 새 Azure Data Explorer 테이블에 데이터를 수집(로드)합니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 25c0bb4071c74c299ab69432ffc18ad50408be46
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644724"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집

> [!div class="op_single_selector"]
> * [컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md)
> * [로컬 파일의 JSON 데이터를 기존 테이블에 수집](one-click-ingestion-existing-table.md)

[원클릭 수집](ingest-data-one-click.md)을 사용하면 JSON, CSV 및 기타 형식의 데이터를 신속하게 테이블로 수집하고 간단하게 매핑 구조를 만들 수 있습니다. 스토리지, 로컬 파일 또는 컨테이너의 데이터를 일회성 또는 지속적인 수집 프로세스로 수집할 수 있습니다.  

이 문서에서는 직관적인 원클릭 마법사를 특정 사례에 사용하여 **컨테이너** 의 **CSV** 데이터를 **새 테이블** 에 수집하는 방법을 설명합니다. 동일한 프로세스를 살짝 변형하여 다양한 사용 사례를 처리할 수 있습니다.

원클릭 수집의 개요와 필수 조건 목록은 [원클릭 수집](ingest-data-one-click.md)을 참조하세요.
Azure Data Explorer의 기존 테이블에 데이터를 수집하는 방법에 대한 자세한 내용은 [기존 테이블로 원클릭 수집](one-click-ingestion-existing-table.md)을 참조하세요.

## <a name="ingest-new-data"></a>새 데이터 수집

1. 웹 UI의 왼쪽 메뉴에서 *데이터베이스* 를 마우스 오른쪽 단추로 클릭하고 **새 데이터 수집** 을 선택합니다.

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="새 데이터 수집":::

1. **새 데이터 수집** 창에서 **원본** 탭이 선택됩니다. 

1. **새 테이블 만들기** 를 선택하고 새 테이블의 이름을 입력합니다. 영숫자, 하이픈 및 밑줄을 사용할 수 있습니다. 특수 문자는 지원되지 않습니다.

    > [!NOTE]
    > 테이블은 1~1024자여야 합니다.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="새 테이블 원클릭 수집 만들기":::

## <a name="select-an-ingestion-type"></a>수집 유형 선택

**수집 형식** 에서 다음 단계를 수행합니다.
   
  1. **컨테이너에서** 를 선택합니다. 
  1. **스토리지에 연결** 필드에서 컨테이너의 [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)을 추가하고 필요한 대로 샘플 크기를 입력합니다. 이 컨테이너 내의 폴더에서 수집하려면 [컨테이너의 폴더에서 수집](#ingest-from-folder-in-a-container)을 참조하세요.

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="컨테이너에서 원클릭 수집":::

     > [!TIP] 
     > **파일에서** 수집하려면 [Azure Data Explorer에서 원클릭 수집을 사용하여 로컬 파일의 JSON 데이터를 기존 테이블에 수집](one-click-ingestion-existing-table.md#select-an-ingestion-type)을 참조하세요.

### <a name="ingest-from-folder-in-a-container"></a>컨테이너의 폴더에서 수집

컨테이너 내의 특정 폴더에서 수집하려면 다음 형식의 문자열을 생성합니다.

*container_path*`/`*folder_path*`;`*access_key_1*

[수집 유형 선택](#select-an-ingestion-type)에서 SAS URL 대신 이 문자열을 사용합니다.

1. 스토리지 계정으로 이동하여 **Storage Explorer > Blob 컨테이너 선택** 을 선택합니다.

    :::image type="content" source="media/one-click-ingestion-new-table/blob-containers.png" alt-text="Azure Storage 계정의 액세스 Blob 컨테이너 스크린샷":::

1. 선택한 폴더로 이동하여 **URL 복사** 를 선택합니다. 이 값을 임시 파일에 붙여넣고 이 문자열의 끝에 `;`을 추가합니다.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-url.png" alt-text="Blob 컨테이너의 폴더에 있는 URL 복사의 스크린샷 - Azure Storage 계정":::

1. **설정** 의 왼쪽 메뉴에서 **액세스 키** 를 선택합니다.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-key-1.png" alt-text="액세스 키 스토리지 계정 복사 키 문자열의 스크린샷":::

1. **키 1** 에서 **키** 문자열을 복사합니다. 2단계에서 문자열의 끝에 이 값을 붙여넣습니다. 

### <a name="storage-subscription-error"></a>스토리지 구독 오류

스토리지 계정에서 수집할 때 다음과 같은 오류 메시지가 표시되는 경우:

> 선택한 구독에서 스토리지를 찾을 수 없습니다. 포털에서 선택한 구독에 스토리지 계정 *`storage_account_name`* 구독을 추가하세요.

1. 오른쪽 메뉴 트레이에서 :::image type="icon" source="media/ingest-data-one-click/directory-subscription-icon.png" border="false"::: 아이콘을 선택합니다. **디렉터리 + 구독** 창이 열립니다.

1. **모든 구독** 드롭다운에서 스토리지 계정의 구독을 선택한 목록에 추가합니다. 

    :::image type="content" source="media/ingest-data-one-click/subscription-dropdown.png" alt-text="구독 드롭다운이 빨간색 상자로 강조 표시된 디렉터리 + 구독 창의 스크린샷":::

## <a name="sample-data"></a>예제 데이터

데이터 샘플이 표시됩니다. 원하는 경우 특정 문자로 시작하거나 끝나는 파일만 수집하도록 데이터를 필터링합니다. 필터를 조정하면 미리 보기가 자동으로 업데이트됩니다.

예를 들어 *.csv* 확장자로 시작하는 모든 파일을 필터링합니다.

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="원클릭 수집 필터":::

## <a name="edit-the-schema"></a>스키마 편집

**스키마 편집** 을 선택하여 테이블 열 구성을 살펴보고 편집합니다. 시스템에서 BLOB 중 하나를 임의로 선택합니다. 그러면 해당 BLOB에 따라 스키마가 생성됩니다. 원본 이름을 살펴보면 원본의 압축 여부를 서비스가 자동으로 식별합니다.

**스키마** 탭에서 다음을 수행합니다.

   1. **데이터 형식** 을 선택합니다.

        이 예제의 데이터 형식은 **CSV** 입니다.

        > [!TIP]
        > **JSON** 파일을 사용하려면 [Azure Data Explorer에서 원클릭 수집을 사용하여 로컬 파일의 JSON 데이터를 기존 테이블에 수집](one-click-ingestion-existing-table.md#edit-the-schema)을 참조하세요.

   1. **열 이름 포함** 확인란을 선택하면 파일의 제목 행을 무시할 수 있습니다.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="[열 이름 포함] 선택":::

**매핑 이름** 필드에 매핑 이름을 입력합니다. 영숫자 및 밑줄을 사용할 수 있습니다. 공백, 특수 문자 및 하이픈은 지원되지 않습니다.

:::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="테이블 매핑 이름 원클릭 수집":::

### <a name="edit-the-table"></a>테이블 편집

새 테이블에 수집하면 테이블을 만들 때 테이블의 다양한 측면을 변경합니다.

테이블에서 다음을 수행합니다. 
 * 편집할 새 열 이름을 두 번 클릭합니다.
 * 새 열 헤더를 선택하고 다음 중 하나를 수행합니다.

    [!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

  > [!NOTE]
  > 테이블 형식의 경우 각 열을 Azure Data Explorer의 한 열에 수집할 수 있습니다.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>수집 시작

**수집 시작** 을 선택하여 테이블 및 매핑을 만들고 데이터 수집을 시작합니다.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="수집 시작 원클릭 수집":::

## <a name="complete-data-ingestion"></a>데이터 수집 완료

데이터 수집이 성공적으로 완료되면 **데이터 수집 완료** 창에서 세 단계 모두 녹색 확인 표시가 나타납니다.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="원클릭 수집 완료"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>컨테이너에 대한 지속적인 수집 만들기

지속적인 수집을 사용하면 원본 컨테이너의 새 파일을 수신 대기하는 이벤트 그리드를 만들 수 있습니다. 미리 정의된 매개 변수 조건(접두사, 접미사 등)을 충족하는 새 파일이 자동으로 대상 테이블에 수집됩니다. 

1. **연속 수집** 타일에서 **Event Grid** 를 선택하여 Azure Portal을 엽니다. 데이터 연결 페이지가 열립니다. 이벤트 그리드 데이터 커넥터가 열리고 원본 및 대상 매개 변수(원본 컨테이너, 테이블 및 매핑)가 이미 입력되어 있습니다.
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="지속적인 수집 단추":::

1. **만들기** 를 선택하여 해당 컨테이너의 변경 내용, 업데이트 또는 새 데이터를 수신 대기하는 데이터 연결을 만듭니다. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Event Hub 연결 만들기":::

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용하여 Azure Data Explorer에 대한 쿼리 작성](write-queries.md)
