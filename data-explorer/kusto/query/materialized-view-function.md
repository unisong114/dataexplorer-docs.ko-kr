---
title: materialized_view () (범위 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 materialized_view () 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: e8dee5e1c33328eb70f984b20f61d4585abae550
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057190"
---
# <a name="materialized_view-function"></a>materialized_view () 함수

[구체화 된 뷰의](../management/materialized-views/materialized-view-overview.md)구체화 된 부분을 참조 합니다. 

`materialized_view()`함수는 사용자가 허용 하는 최대 대기 시간을 지정 하는 동시에 뷰의 *구체화* 된 부분만 쿼리 하는 방법을 지원 합니다. 이 옵션은 최신 레코드를 반환 하는 것을 보장 하지는 않지만 항상 전체 뷰를 쿼리 하는 것 보다 성능이 더 우수 합니다. 이 함수는 원격 분석 대시보드 등의 성능에 대 한 몇 가지 최신 유효성을 희생 하려는 시나리오에 유용 합니다.

<!--- csl --->
```
materialized_view('ViewName')
```

## <a name="syntax"></a>구문

`materialized_view(`*ViewName* `,` [*max_age*]`)`

## <a name="arguments"></a>인수

* *ViewName*:의 이름 `materialized view` 입니다.
* *max_age*: 선택 사항입니다. 지정 하지 않으면 뷰의 *구체화* 된 부분만 반환 됩니다. 제공 된 경우 함수는 마지막 구체화 시간이 [-max_age] 보다 크면 뷰의 _구체화_ 된 부분을 반환 합니다 @now . 그렇지 않으면 전체 뷰가 반환 됩니다 ( *ViewName* 를 직접 쿼리 하는 것과 동일). 

## <a name="examples"></a>예

뷰의 *구체화* 된 부분만 마지막으로 구체화 된 시기에 독립적으로 쿼리 합니다.

<!-- csl -->
```
materialized_view("ViewName")
```

마지막 10 분 동안 구체화 된 경우에만 *구체화* 된 파트를 쿼리 합니다. 구체화 된 파트가 10 분 보다 오래 된 경우 전체 뷰를 반환 합니다. 이 옵션은 구체화 된 파트를 쿼리 하는 것 보다 성능이 떨어질 수 있습니다.

<!-- csl -->
```
materialized_view("ViewName", 10m)
```

## <a name="notes"></a>참고

* 뷰를 만든 후에는 클러스터 간/데이터베이스 간 쿼리에 참여 하는 것을 포함 하 여 데이터베이스의 다른 테이블과 마찬가지로 쿼리를 수행할 수 있습니다.
* 구체화 된 뷰는 와일드 카드 공용 구조체 또는 검색에 포함 되지 않습니다.
* 뷰를 쿼리 하는 구문은 뷰 이름 (예: 테이블 참조)입니다.
* 구체화 된 뷰를 쿼리하면 원본 테이블에 수집 모든 레코드를 기반으로 하 여 항상 최신 결과가 반환 됩니다. 이 쿼리는 뷰의 구체화 된 부분을 원본 테이블의 구체화 되지 않은 모든 레코드와 결합 합니다. 자세한 내용은 [뒷부분](../management/materialized-views/materialized-view-overview.md#how-materialized-views-work) 에 나오는 항목을 참조 하세요.
