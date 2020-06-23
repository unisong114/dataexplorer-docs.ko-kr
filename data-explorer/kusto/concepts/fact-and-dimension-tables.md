---
title: 팩트 및 차원 테이블-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 팩트 및 차원 테이블에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 6db37366ddd3d70aaa89c0d6eebd1ec8affbb76d
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264446"
---
# <a name="fact-and-dimension-tables"></a>팩트 및 차원 테이블

Azure 데이터 탐색기 데이터베이스에 대 한 스키마를 디자인할 때는 테이블을 두 범주 중 하나에 속하는 것으로 간주 합니다.
* [팩트 테이블](https://en.wikipedia.org/wiki/Fact_table)
* [차원 테이블](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>팩트 테이블
팩트 테이블은 레코드를 변경할 수 없는 "팩트" (예: 서비스 로그 및 측정 정보)가 있는 테이블입니다. 레코드는 스트리밍 방식이 나 대량의 청크로 테이블에 점진적으로 추가 됩니다. 레코드는 비용이 나 값이 손실 되어 제거 될 때까지 유지 됩니다. 그렇지 않으면 레코드가 업데이트 되지 않습니다.

엔터티 데이터는 팩트 테이블에 저장 되는 경우가 있습니다 .이 경우 엔터티 데이터는 느리게 변경 됩니다. 예를 들어 일부 실제 엔터티에 대 한 데이터 (예: 가끔 위치를 변경 하지 않는 office 장비)에 대 한 데이터입니다.
Kusto의 데이터는 변경할 수 없으므로 일반적으로 각 테이블에 두 개의 열을 포함 하는 것이 좋습니다.
* `string`엔터티를 식별 하는 id () 열입니다.
* 마지막으로 수정한 ( `datetime` ) 타임 스탬프 열

그러면 각 엔터티 id의 마지막 레코드만 검색 됩니다.

## <a name="dimension-tables"></a>차원 테이블
차원 테이블:
* 엔터티 식별자에서 해당 속성으로 조회 테이블과 같은 참조 데이터를 유지 합니다.
* 단일 트랜잭션에서 전체 내용이 변경 되는 테이블의 스냅숏 유사 데이터를 유지 합니다.

차원 테이블은 새 데이터로 정기적으로 수집 되지 않습니다. 대신 전체 데이터 콘텐츠는 같은 작업을 사용 하 여 한 번에 업데이트 됩니다. [set 또는-replace](../management/data-ingestion/ingest-from-query.md), [. 익스텐트 이동](../management/extents-commands.md#move-extents)또는 [. 테이블 이름 바꾸기](../management/rename-table-command.md)

경우에 따라 팩트 테이블에서 차원 테이블이 파생 될 수도 있습니다. 각 엔터티에 대 한 마지막 레코드를 사용 하는 테이블에 대 한 쿼리를 사용 하 여 팩트 테이블의 [업데이트 정책을](../management/updatepolicy.md) 통해이 프로세스를 수행할 수 있습니다.

## <a name="differentiate-fact-and-dimension-tables"></a>팩트 및 차원 테이블 구분

Kusto에는 팩트 테이블과 차원 테이블을 구분 하는 프로세스가 있습니다. 그 중 하나는 [연속 내보내기](../management/data-export/continuous-data-export.md)입니다.

이러한 메커니즘은 팩트 테이블의 데이터를 정확히 한 번 처리 하는 것을 보장 합니다. [데이터베이스 커서](../management/databasecursor.md) 메커니즘을 사용 합니다.

예를 들어 연속 내보내기 작업을 실행할 때마다는 데이터베이스 커서의 마지막 업데이트 이후 수집 된 모든 레코드를 내보냅니다. 연속 내보내기 작업은 팩트 테이블과 차원 테이블을 구분 해야 합니다. 팩트 테이블은 새로 수집 데이터만 처리 하 고, 차원 테이블은 조회로 사용 됩니다. 따라서 전체 테이블을 고려해 야 합니다.

테이블을 "팩트 테이블" 또는 "차원 테이블"로 "표시" 할 수 있는 방법은 없습니다.
데이터가 테이블로 수집 되는 방법 및 테이블을 사용 하는 방법은 해당 유형을 식별 하는 것입니다.
