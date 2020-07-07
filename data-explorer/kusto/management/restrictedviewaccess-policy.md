---
title: Kusto RestrictedViewAccess 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 RestrictedViewAccess 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967607"
---
# <a name="restricted_view_access-policy-command"></a>restricted_view_access 정책 명령

*RestrictedViewAccess* 정책은 [여기](../management/restrictedviewaccesspolicy.md)에 설명 되어 있습니다.

데이터베이스의 테이블에서 정책을 사용 하거나 사용 하지 않도록 설정 하는 제어 명령은 다음과 같습니다.

정책을 사용 하거나 사용 하지 않도록 설정 하려면:
```kusto
.alter table TableName policy restricted_view_access true|false
```

여러 테이블의 정책을 설정 하거나 해제 하려면:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

정책을 보려면 다음을 수행 합니다.
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

정책을 삭제 하려면 다음을 사용 하지 않도록 설정 하는 것과 동일 합니다.
```kusto
.delete table TableName policy restricted_view_access  
```