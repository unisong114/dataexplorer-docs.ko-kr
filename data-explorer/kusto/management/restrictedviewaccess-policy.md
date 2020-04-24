---
title: 제한보기액세스 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 제한된 ViewAccess 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520454"
---
# <a name="restrictedviewaccess-policy"></a>제한뷰액세스 정책

*제한뷰액세스* 정책은 [여기에 설명되어 있습니다.](../management/restrictedviewaccesspolicy.md)

데이터베이스의 테이블에서 정책을 사용하거나 사용하지 않도록 설정하기 위한 제어 명령은 다음과 같습니다.

정책을 사용/사용하지 않으려면 다음을 수행하십시오.
```kusto
.alter table TableName policy restricted_view_access true|false
```

여러 테이블의 정책을 활성화/비활성화하려면 다음을 수행하십시오.
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

정책을 보려면 다음을 수행하십시오.
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

정책을 삭제하려면(비활성화에 해당):
```kusto
.delete table TableName policy restricted_view_access  
```