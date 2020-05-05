---
title: 원클릭 수집을 사용하여 Azure Data Explorer에 데이터 수집
description: 원클릭 수집을 사용하여 간단하게 Azure Data Explorer에 데이터를 수집(로드)하는 방법에 대한 개요입니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5f424716a9227f22b4ec5b148d29798b0a55fa27
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618380"
---
# <a name="what-is-one-click-ingestion"></a>원클릭 수집이란? 

원클릭 수집을 사용하면 Azure Data Explorer의 데이터 원본을 기반으로 신속하게 데이터를 수집하고 테이블 및 매핑 구조를 자동으로 제안할 수 있습니다. 

Azure Data Explorer 웹 UI를 사용하여 스토리지(BLOB 파일), 로컬 파일 또는 컨테이너의 데이터를 수집할 수 있습니다(BLOB 10,000개까지). 연속 수집을 위해 컨테이너에서 이벤트 그리드를 정의할 수도 있습니다. JSON, CSV 또는 [다른 형식](#file-formats)의 기존 또는 새 테이블에 데이터를 수집할 수 있습니다. 원클릭 수집은 데이터 원본을 기반으로 새 테이블 및 테이블 매핑의 구조를 제안하고, 기존 테이블 및 새 테이블의 테이블 매핑과 테이블 구조를 조정하는 직관적인 플랫폼을 제공할 수 있습니다. 원클릭 수집은 불과 몇 분 내에 테이블에 데이터를 수집합니다.

원클릭 수집은 데이터를 처음으로 수집하거나 데이터의 스키마가 익숙하지 않은 경우에 특히 유용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.
* [Azure Data Explorer 웹 UI](https://dataexplorer.azure.com/)에 로그인하여 [클러스터에 연결을 추가](/azure/data-explorer/web-query-data#add-clusters)합니다.

## <a name="file-formats"></a>파일 형식

원클릭 수집은 다음 형식의 원본 데이터에서 새 테이블을 수집할 수 있습니다.
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>원클릭 수집 마법사

원클릭 수집 마법사는 원클릭 수집 프로세스를 안내합니다. 

> [!Note]
> 이 섹션에서는 마법사의 일반적인 내용을 설명합니다. 선택하는 옵션은 데이터를 새 테이블에 수집하는지 아니면 기존 테이블에 수집하는지에 따라 달라집니다. 자세한 내용은 다음을 참조하세요.
    > * [새 테이블](one-click-ingestion-new-table.md)에 수집
    > * [기존 테이블](one-click-ingestion-existing-table.md)에 수집 
    
1. 마법사에 액세스하려면 Azure Data Explorer 웹 UI의 왼쪽 메뉴에서 *데이터베이스* 또는 *테이블*을 마우스 오른쪽 단추로 클릭하고 **새 데이터 수집(미리 보기)** 을 선택합니다.

    ![웹 UI에서 원클릭 수집 선택](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. 이 마법사는 다음 옵션을 안내합니다.
    * [기존 테이블](one-click-ingestion-existing-table.md)에 수집
    * [새 테이블](one-click-ingestion-new-table.md)에 수집
    * 다음에서 데이터를 수집합니다.
      * Blob Storage
      * 로컬 파일
      * 컨테이너
    * 1~10,000 사이에서 같은 크기 입력(컨테이너만 해당)
       
1. 데이터 원본을 성공적으로 선택하면 데이터 미리 보기가 표시됩니다. 
    컨테이너에서 데이터를 수집하는 경우 특정 접두사 또는 파일 확장명을 가진 파일만 수집하도록 데이터를 필터링할 수 있습니다. 예를 들어 파일 이름이 *Europe*이라는 단어로 시작하는 파일 또는 확장명이 *.json*인 파일만 수집할 수 있습니다. 

1. **스키마 편집**을 클릭합니다. 자동 생성된 스키마 및 수집 속성(데이터 형식, 매핑 이름 등)을 검토하고 조정합니다.

1. 데이터 수집 프로세스를 시작합니다.

> [!Note]
> 데이터 원본이 컨테이너인 경우 Azure Data Explorer의 데이터 수집 집계(일괄 처리) 정책은 수집 프로세스를 최적화하도록 설계되었다는 점에 주의해야 합니다. 기본적으로 이 정책은 5분 또는 데이터 500MB로 구성되므로 대기 시간이 발생할 수 있습니다. 집계 옵션에 대한 내용은 [일괄 처리 정책](kusto/management/batchingpolicy.md)을 참조하세요. 다른 원본에서 데이터를 수집하는 경우 수집이 즉시 적용됩니다.

## <a name="next-steps"></a>다음 단계

* 원클릭 수집을 사용하여 데이터를 [기존 테이블](one-click-ingestion-existing-table.md)에 수집할 것인지 아니면 [새 테이블](one-click-ingestion-new-table.md)에 수집할 것인지 결정하세요.
* [Azure Data Explorer 웹 UI에서 데이터 쿼리](/azure/data-explorer/web-query-data)
* [Kusto 쿼리 언어를 사용하여 Azure Data Explorer에 대한 쿼리 작성](/azure/data-explorer/write-queries)
