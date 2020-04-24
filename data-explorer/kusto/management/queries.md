---
title: 쿼리 관리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 관리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520522"
---
# <a name="queries-management"></a>쿼리 관리

## <a name="show-queries"></a>.show 쿼리

명령은 `.show` `queries` 최종 상태에 도달하고 명령을 호출하는 사용자가 볼 수 있는 액세스 권한이 있는 쿼리 목록을 반환합니다.


* [데이터베이스 관리자 또는 데이터베이스 모니터는](../management/access-control/role-based-authorization.md) 데이터베이스에서 호출된 모든 명령을 볼 수 있습니다.
* 다른 사용자는 호출된 쿼리만 볼 수 있습니다.

**구문**

`.show` `queries`

## <a name="show-running-queries"></a>실행 중인 쿼리를 .show

`.show` `running` 명령은 `queries` 사용자 또는 다른 사용자 또는 모든 사용자가 현재 실행 중인 쿼리 목록을 반환합니다.

**구문**

```kusto
.show running queries
```

* (1) 호출하는 사용자가 현재 실행 중인 쿼리를 반환합니다(읽기 액세스 필요).

## <a name="cancel-query"></a>.cancel 쿼리

명령은 `.cancel` `query` 동일한 사용자가 이전에 시작한 특정 쿼리를 취소하는 최선의 시도를 시작합니다.

* 클러스터 관리자는 실행 중인 쿼리를 취소할 수 있습니다.
* 데이터베이스 관리자는 관리자 액세스 권한이 있는 데이터베이스에서 호출된 실행 중인 쿼리를 취소할 수 있습니다.
* 다른 사용자는 시작한 쿼리만 취소할 수 있습니다. 

**구문**

`.cancel``query` *클라이언트 요청 ID*

* *ClientRequestId는* 원래 쿼리 ClientRequestId 필드의 값입니다. `string`

**예제**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

