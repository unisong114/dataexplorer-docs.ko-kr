---
title: 제한보기액세스 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 제한된 ViewAccess 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520420"
---
# <a name="restrictedviewaccess-policy"></a>제한뷰액세스 정책

*제한ViewAccess* 데이터베이스의 테이블에서 사용할 수 있는 선택적 정책입니다.

테이블에서 이 정책을 사용하도록 설정하면 테이블의 데이터는 데이터베이스의 [Un제한Viewer](../management/access-control/role-based-authorization.md) 역할에 추가된 보안 *주체에 대해서만* 쿼리할 수 있습니다.

테이블에서 정책을 사용하도록 설정하면 [Un제한Viewer](../management/access-control/role-based-authorization.md) 데이터베이스 수준 역할에 포함되지 않은 보안 주체(테이블/데이터베이스/클러스터 관리자)는 테이블에서 데이터를 쿼리할 수 없습니다.

[Un무제한Viewer](../management/access-control/role-based-authorization.md) 역할은 현재 주 서버가 데이터베이스(데이터베이스 관리자/사용자/뷰어)를 쿼리할 권한이 이미 있다고 가정하여 정책이 활성화된 데이터베이스의 *모든* 테이블에 대한 보기 권한을 부여합니다. 역할에 또는 역할에서 보안 주체를 추가하거나 제거하는 것은 [DatabaseAdmin](../management/access-control/role-based-authorization.md)에서 수행할 수 있습니다.

> [!NOTE]
> 제한ViewAccess 정책은 [행 수준 보안 정책이](./rowlevelsecuritypolicy.md) 활성화된 테이블에서 구성할 수 없습니다.

제한된 ViewAccess 정책을 관리하기 위한 제어 명령에 대한 자세한 내용은 [여기를 참조하십시오.](../management/restrictedviewaccess-policy.md)