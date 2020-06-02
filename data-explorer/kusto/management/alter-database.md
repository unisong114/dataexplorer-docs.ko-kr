---
title: . alter database prettyname-Azure 데이터 탐색기
description: 이 문서에서는 `.alter` 데이터베이스의 예쁜 이름 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294511"
---
# <a name="alter-database-prettyname"></a>. alter database prettyname

데이터베이스의 친근 한 이름을 변경 합니다.

[Databaseadmin 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.alter``database` *DatabaseName* `prettyname` DatabaseName `'` *DatabasePrettyName*`'`

**반환 출력**
 
|출력 매개 변수 |유형 |Description 
|---|---|---
|DatabaseName |String |데이터베이스의 이름입니다.
|PrettyName |String |데이터베이스의 이름입니다.
