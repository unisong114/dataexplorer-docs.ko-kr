---
title: 엔터티 참조 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 엔터티 참조에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509404"
---
# <a name="entity-references"></a>엔터티 참조

명명된 Kusto 스키마 엔터티(데이터베이스, 테이블, 열 및 저장된 함수는 클러스터가 아닌)는 해당 이름을 사용하여 쿼리에서 참조됩니다. 엔터티의 컨테이너가 현재 컨텍스트에서 명확하지 않은 경우 추가 자격 없이 엔터티 이름을 사용할 수 있습니다. 예를 들어 , 라는 `DB`데이터베이스에 대해 쿼리를 실행할 때 `T` 단순히 이름을 사용하여 해당 `T`데이터베이스에서 호출된 테이블을 참조할 수 있습니다.

반면에 엔터티의 컨테이너를 컨텍스트에서 사용할 수 없거나 컨텍스트에서 컨테이너와 다른 컨테이너의 엔터티를 참조하려는 경우 엔터티 이름을 컨테이너이름과 컨테이너(및 해당 컨테이너 등)에 연결하는 엔터티의 **정규화된 이름을**사용해야 합니다. 따라서 `DB` 데이터베이스에 대해 실행되는 쿼리는 을 `T1` 사용하여 `DB1` `database("DB1").T1`동일한 클러스터의 다른 데이터베이스에 있는 테이블을 참조할 수 있으며, 다른 클러스터에서 테이블을 참조하려는 경우 를 사용하여 `cluster("https://C2.kusto.windows.net/").database("DB2").T2`할 수 있습니다.

엔터티 참조는 엔터티 컨테이너의 컨텍스트에서 고유한 한 엔터티 꽤 이름을 지정할 수도 있습니다. [엔터티 예쁜 이름을](./entity-names.md#entity-pretty-names)참조하십시오.

## <a name="wildcard-matching-for-entity-names"></a>엔터티 이름에 대한 와일드카드 일치

일부 컨텍스트에서는 엔터티 이름의 전체 또는`*`일부를 일치시키기 위해 와일드카드()를 사용할 수 있습니다. 예를 들어 다음 쿼리는 현재 데이터베이스의 모든 테이블과 다음 `DB` 이름으로 시작하는 `T`데이터베이스의 모든 테이블을 참조합니다.

```kusto
union *, database("DB1").T*
```

참고: 와일드카드 일치는 달러 기호()로`$`시작하는 엔터티 이름과 일치하지 않습니다.
이러한 이름은 시스템 예약입니다.



