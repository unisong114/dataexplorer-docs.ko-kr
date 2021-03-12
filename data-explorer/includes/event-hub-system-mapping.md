---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: 52a6de4c8ecb3ccbe4e315145b8ae8a3c314ebfc
ms.sourcegitcommit: 600c3430c00eb62d52540fe08dab386a860193cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102472209"
---
> [!NOTE]
> * 시스템 속성은 `json` 및 테이블 형식(`csv`, `tsv` 등)에 대해 지원되며 압축된 데이터에서는 지원되지 않습니다. 지원되지 않는 형식을 사용하는 경우 데이터는 계속 수집되지만, 속성은 무시됩니다.
> * 테이블 형식 데이터의 경우 시스템 속성은 단일 레코드 이벤트 메시지에 대해서만 지원됩니다.
> * JSON 데이터의 경우 시스템 속성은 여러 레코드 이벤트 메시지에 대해서도 지원됩니다. 이러한 경우 시스템 속성은 이벤트 메시지의 첫 번째 레코드에만 추가됩니다. 
> * `csv` 매핑의 경우 속성은 [시스템 속성](../ingest-data-event-hub-overview.md#system-properties) 테이블에 나열된 순서대로 레코드 시작 부분에 추가됩니다.
> * `json` 매핑의 경우 속성은 [시스템 속성](../ingest-data-event-hub-overview.md#system-properties) 테이블의 속성 이름에 따라 추가됩니다.
