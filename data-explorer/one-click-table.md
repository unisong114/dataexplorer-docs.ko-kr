---
title: Azure 데이터 탐색기에서 테이블 만들기
description: 한 번의 클릭 환경을 사용 하 여 Azure 데이터 탐색기에서 테이블을 쉽게 만드는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/06/2020
ms.openlocfilehash: 3302e7cb94a2184be64664f3c3d8698b8bea7643
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402748"
---
# <a name="create-a-table-in-azure-data-explorer-preview"></a>Azure 데이터 탐색기에서 테이블 만들기 (미리 보기)

Azure 데이터 탐색기에서 [데이터](ingest-data-overview.md) 수집 및 [쿼리](write-queries.md) 프로세스의 중요 한 단계는 테이블을 만드는 것입니다. [Azure 데이터 탐색기에서 클러스터 및 데이터베이스를 만든](create-cluster-database-portal.md)후에는 테이블을 만들 수 있습니다. 다음 문서에서는 Azure 데이터 탐색기 웹 UI를 사용 하 여 테이블과 스키마 매핑을 쉽고 빠르게 만드는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.
* [Azure Data Explorer 웹 UI](https://dataexplorer.azure.com/)에 로그인하여 [클러스터에 연결을 추가](web-query-data.md#add-clusters)합니다.

## <a name="create-a-table"></a>테이블 만들기

1. 웹 UI의 왼쪽 메뉴에서 **ExampleDB** 를 마우스 오른쪽 단추로 클릭 하 고 데이터베이스 이름을 마우스 오른쪽 단추로 클릭 한 다음 **테이블 만들기 (미리 보기)** 를 선택 합니다.

    :::image type="content" source="./media/one-click-table/create-table.png" alt-text="Azure 데이터 탐색기 웹 UI에서 테이블 만들기":::

**원본** 탭이 선택 된 상태에서 **테이블 만들기** 창이 열립니다.
1. 데이터베이스 **필드는** 데이터베이스에 자동으로 채워집니다. 드롭다운 메뉴에서 다른 데이터베이스를 선택할 수 있습니다.
1. 테이블 **이름**에 테이블의 이름을 입력 합니다. 
    > [!TIP]
    >  표 이름은 영숫자, 하이픈 및 밑줄을 포함 하 여 최대 1024 자까지 사용할 수 있습니다. 특수 문자는 지원되지 않습니다.

### <a name="select-source-type"></a>원본 유형 선택

1. **원본 유형**에서 테이블 매핑을 만드는 데 사용할 데이터 원본을 선택 합니다. **Blob**, **파일**또는 **컨테이너**에서 다음 옵션 중 하나를 선택 합니다.
   
    
    * **컨테이너**를 사용 하는 경우:
        * Blob의 저장소 url을 입력 하 고 필요에 따라 샘플 크기를 입력 합니다. 
        * **파일**필터를 사용 하 여 파일을 필터링 합니다. 
        * 다음 단계에서 스키마를 정의 하는 데 사용할 파일을 선택 합니다.

        :::image type="content" source="media/one-click-table/storage.png" alt-text="Blob을 사용 하 여 테이블을 만들어 스키마 매핑 만들기":::
    
    * **로컬 파일**을 사용 하는 경우:
        * **찾아보기**를 선택하여 파일을 찾거나, 파일을 필드로 끕니다.

        :::image type="content" source="./media/one-click-table/data-from-file.png" alt-text="로컬 파일의 데이터를 기반으로 테이블 만들기 ":::

    * **Blob**을 사용 하는 경우:
        * **저장소에 대 한 링크** 필드에서 컨테이너의 [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 을 추가 하 고 필요에 따라 샘플 크기를 입력 합니다. 

1. 스키마 **편집** 을 선택 하 여 **스키마** 탭을 계속 진행 합니다.

### <a name="edit-schema"></a>스키마 편집

**스키마** 탭에서 [데이터 형식](ingest-data-one-click.md#file-formats) 및 압축은 왼쪽 창에서 자동으로 식별 됩니다. 잘못 식별 된 경우 **데이터 서식** 드롭다운 메뉴를 사용 하 여 올바른 형식을 선택 합니다.

   * 데이터 형식이 JSON 인 경우에는 1부터 10 까지의 JSON 수준도 선택 해야 합니다. 이 수준에 따라 테이블 열 데이터 분할이 결정됩니다.
   * 데이터 형식이 CSV 인 경우 파일의 머리글 행을 무시 하는 **열 이름을 포함** 하는 확인란을 선택 합니다.

        :::image type="content" source="./media/one-click-table/schema-tab.png" alt-text="Azure의 한 번 클릭 환경에서 create table의 스키마 탭 편집 데이터 탐색기":::
 
1. **매핑**에이 테이블의 스키마 매핑에 대 한 이름을 입력 합니다. 
    > [!TIP]
    >  테이블 이름에는 영숫자 문자와 밑줄이 포함 될 수 있습니다. 공백, 특수 문자 및 하이픈은 지원되지 않습니다.
1. **만들기**를 선택합니다.

## <a name="create-table-completed-window"></a>테이블 만들기 완료 창

테이블 만들기가 성공적으로 완료 되 면 **테이블 만들기 완료** 창에서 두 단계가 모두 녹색 확인 표시로 표시 됩니다.

* **보기 명령** 을 선택 하 여 각 단계에 대 한 편집기를 엽니다. 
    * 편집기에서는 사용자 입력으로부터 생성된 자동 명령을 살펴보고 복사할 수 있습니다.
    
    :::image type="content" source="./media/one-click-table/table-completed.png" alt-text="한 번의 클릭으로 테이블 만들기에서 완료 된 테이블 만들기-Azure 데이터 탐색기":::
 
**테이블 만들기** 진행률 아래의 타일에서 **빠른 쿼리** 또는 **도구**를 탐색 합니다.

* **빠른 쿼리**에는 예제 쿼리가 있는 웹 UI에 대한 링크가 포함되어 있습니다.

* **도구** 에는 관련 된 drop 명령을 실행 **취소할** 수 있는 링크가 포함 되어 있습니다.

> [!NOTE]
> . Drop 명령을 사용 하면 데이터가 손실 될 수 있습니다.<br>
> 이 워크플로의 drop 명령은 create table 프로세스 (새 테이블 및 스키마 매핑)에서 수행한 변경 내용만 되돌립니다.

## <a name="next-steps"></a>다음 단계

* [데이터 수집 개요](ingest-data-overview.md)
* [한 번 클릭으로 수집](ingest-data-one-click.md)
* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)  
