---
title: Kusto RestrictedViewAccess 정책 관리-Azure 데이터 탐색기
description: Azure 데이터 탐색기의 RestrictedViewAccess 정책 명령에 대해 알아봅니다. 이 정책을 확인, 활성화, 비활성화, 변경 및 삭제 하는 방법을 참조 하세요.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002920"
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
