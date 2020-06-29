---
title: 엔터티 참조-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 엔터티 참조에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d652ea8551a21d542ad6afef575616e7387183f
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517889"
---
# <a name="entity-references"></a>엔터티 참조

이름을 사용 하 여 쿼리에서 Kusto 스키마 엔터티를 참조 합니다. 유효한 엔터티 이름에는 *데이터베이스*, *테이블*, *열*및 저장 된 함수가 있습니다. *클러스터* 는 이름으로 참조할 수 없습니다.
현재 컨텍스트에서 엔터티의 컨테이너가 명확 하지 않은 경우 추가 자격 없이 엔터티 이름을 사용 합니다. 예를 들어 라는 데이터베이스에 대해 쿼리를 실행 하는 경우 해당 `DB` 데이터베이스에서 라는 테이블을 이름으로 참조할 수 있습니다 `T` `T` .

컨텍스트에서 엔터티의 컨테이너를 사용할 수 없는 경우 또는 컨텍스트의 컨테이너와 다른 컨테이너에서 엔터티를 참조 하려는 경우 엔터티의 **정규화 된 이름을**사용 합니다.
이 이름은 엔터티 이름과 컨테이너의 컨테이너와 같은 방식으로 연결 됩니다. 이러한 방식으로 데이터베이스에 대해 실행 되는 쿼리는를 `DB` 사용 하 `T1` `DB1` 여 동일한 클러스터의 다른 데이터베이스에 있는 테이블을 참조할 수 있습니다 `database("DB1").T1` . 쿼리에서 다른 클러스터의 테이블을 참조 하려는 경우, 예를 들어를 사용 하 여 수행할 수 있습니다 `cluster("https://C2.kusto.windows.net/").database("DB2").T2` .

엔터티 참조는 엔터티 컨테이너의 컨텍스트에서 고유 하기만 하면 엔터티 이름을 사용할 수도 있습니다. 자세한 내용은 [엔터티 이름](./entity-names.md#entity-pretty-names)을 참조 하세요.

## <a name="wildcard-matching-for-entity-names"></a>엔터티 이름에 대 한 와일드 카드 일치

일부 컨텍스트에서는 와일드 카드 ()를 사용 `*` 하 여 엔터티 이름의 전체 또는 일부를 일치 시킬 수 있습니다. 예를 들어 다음 쿼리는 현재 데이터베이스의 모든 테이블과 이름이로 시작 하는 데이터베이스의 모든 테이블을 참조 합니다 `DB` `T` .

```kusto
union *, database("DB1").T*
```

> [!NOTE]
> 와일드 카드 일치는 달러 기호 ()로 시작 하는 엔터티 이름과 일치할 수 없습니다 `$` .
이러한 이름은 시스템에서 예약 됩니다.

## <a name="next-steps"></a>다음 단계

* [스키마 엔터티 형식](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/)
* [스키마 엔터티 이름](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)
