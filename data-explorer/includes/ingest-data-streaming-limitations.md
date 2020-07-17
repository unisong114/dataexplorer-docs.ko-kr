---
title: 파일 포함
description: 포함 파일
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 33168f001dd3d998fbf82169c7637e2eb390e0bc
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423157"
---
## <a name="limitations"></a>제한 사항

* 데이터베이스 자체 또는 해당 테이블에 [스트리밍 수집 정책이](../kusto/management/streamingingestionpolicy.md) 정의 되 고 사용 하도록 설정 된 경우 데이터베이스 [커서](../kusto/management/databasecursor.md) 는 데이터베이스에 대해 지원 되지 않습니다.
* 스트리밍 수집에 사용할 [데이터 매핑을](../kusto/management/mappings.md) [미리 만들어야](../kusto/management/create-ingestion-mapping-command.md) 합니다. 개별 스트리밍 수집 요청은 인라인 데이터 매핑을 허용 하지 않습니다.
* VM 및 클러스터 크기를 늘려 스트리밍 수집 성능 및 용량을 확장 합니다. 동시 수집 요청 수는 코어 당 6 개로 제한 됩니다. 예를 들어 D14 및 L16와 같은 16 개 코어 Sku의 경우 지원 되는 최대 부하는 동시 수집 요청 96입니다. 코어 2와 같은 두 코어 Sku의 경우 지원 되는 최대 부하는 12 개의 동시 수집 요청입니다.
* 스트리밍 수집 요청에 대 한 데이터 크기 제한은 4mb입니다.
* 테이블 생성 및 수정과 수집 매핑 등의 스키마 업데이트는 스트리밍 수집 서비스에 대해 최대 5 분이 걸릴 수 있습니다. 자세한 내용은 스트리밍 수집 [및 스키마 변경](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md)을 참조 하세요.
* 데이터가 스트리밍을 통해 수집 않는 경우에도 클러스터에서 스트리밍 수집을 사용 하도록 설정 하면에서 클러스터 컴퓨터의 로컬 SSD 디스크의 일부를 사용 하 여 수집 데이터를 스트리밍하 고 핫 캐시에 사용할 수 있는 저장소를 줄입니다.
* 스트리밍 수집 데이터에 [익스텐트 태그](../kusto/management/extents-overview.md#extent-tagging) 를 설정할 수 없습니다.
* 데이터베이스의 테이블에서 스트리밍 수집을 사용 하는 경우에는이 데이터베이스를 [종동체 데이터베이스](../follower.md) 의 리더 또는 Azure 데이터 공유에 대 한 [데이터 공급자로](../data-share.md#data-provider---share-data) 사용할 수 없습니다.
