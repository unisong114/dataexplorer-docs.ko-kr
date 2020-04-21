---
title: 팩트 및 차원 표 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 팩트 및 차원 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a88f8549bf5accce197294d433ece8ccb683281
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523242"
---
# <a name="fact-and-dimension-tables"></a>팩트 및 차원 테이블

Kusto 데이터베이스에 대한 스키마를 디자인할 때 테이블을 두 가지 범주 중 하나에 속하는 것으로 간주하는 것이 유용합니다.
* [팩트 테이블](https://en.wikipedia.org/wiki/Fact_table) 및
* [차원 테이블](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table).

**팩트 테이블은** 서비스 로그 및 측정 정보와 같이 레코드가 변경할 수 없는 "팩트"인 테이블입니다. 레코드는 점진적으로(스트리밍 방식으로 또는 큰 청크)에 테이블에 추가되며 비용 상의 이유로 제거해야 하거나 가치를 잃을 때까지 테이블에 보관됩니다. 그렇지 않으면 레코드가 업데이트되지 않습니다.

**차원 테이블에는** 참조 데이터(예: 엔터티 식별자에서 해당 속성에 대한 조회 테이블) 및 스냅숏과 같은 데이터(단일 트랜잭션에서 전체 내용이 변경되는 테이블)를 보유합니다. 일반적으로 이러한 테이블은 새 데이터로 정기적으로 수집되지 않습니다. 대신 [.set-or-replaces](../management/data-ingestion/ingest-from-query.md), [.move 익스텐트](../management/extents-commands.md#move-extents)또는 [.name table](../management/rename-table-command.md)과 같은 작업을 사용하여 전체 데이터 콘텐츠가 한 번에 업데이트됩니다.
경우에 따라 차원 테이블은 팩트 테이블의 [업데이트 정책과](../management/updatepolicy.md) 각 엔터티에 대한 마지막 레코드를 차지하는 테이블에 대한 일부 쿼리를 통해 팩트 테이블에서 파생될 수 있습니다.

Kusto의 테이블을 팩트 테이블 또는 차원 테이블로 "표시"할 방법이 없다는 것을 깨닫는 것이 중요합니다. 데이터가 테이블에 수집되는 방법과 테이블이 사용되는 방식이 중요합니다.

> [!NOTE]
> 때로는 Kusto가 엔터티 데이터가 느리게 변경되도록 팩트 테이블에 엔터티 데이터를 보유하는 데 사용됩니다. 예를 들어 위치를 자주 변경하지 않는 일부 물리적 엔터티(예: 사무실 장비)에 대한 데이터입니다.
> Kusto의 데이터는 변경할 수 없기 때문에 각 테이블에 엔터티를 식별하는 ID ()`string`열과 마지막으로 수정된()`datetime`타임스탬프 열이라는 두 개의 열을 보유하도록 하는 것이 일반적입니다. 그런 다음 각 엔터티 ID에 대한 마지막 레코드만 검색됩니다.



## <a name="commands-that-differentiate-fact-and-dimension-tables"></a>팩트 및 차원 테이블을 구분하는 명령

Kusto에는 팩트 테이블과 차원 테이블을 구분하는 프로세스가 있습니다.

* [지속적인 내보내기](../management/data-export/continuous-data-export.md)




이러한 프로세스는 데이터베이스 커서 메커니즘에 의존하여 팩트 테이블에서 데이터를 정확하게 한 번 처리하도록 [보장됩니다.](../management/databasecursor.md)

예를 들어 연속 내보내기 작업의 모든 실행은 데이터베이스 커서의 마지막 업데이트 이후 받은 모든 레코드를 내보냅니다. 따라서 연속 내보내기 작업은 팩트 테이블(새로 수집된 데이터만 처리)과 차원 테이블(조회로 사용됨)을 구분해야 하므로 전체 테이블을 고려해야 합니다.