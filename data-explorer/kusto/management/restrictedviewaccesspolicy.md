---
title: Kusto RestrictedViewAccess 정책 컨트롤 쿼리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 RestrictedViewAccess 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617429"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess 정책

*RestrictedViewAccess* 는 데이터베이스의 테이블에서 사용 하도록 설정할 수 있는 선택적 정책입니다.

테이블에서이 정책을 사용 하는 경우 테이블의 데이터는 데이터베이스의 [UnrestrictedViewer](../management/access-control/role-based-authorization.md) 역할에 추가 된 보안 주체로 *만* 쿼리할 수 있습니다.

테이블에서 정책을 사용 하도록 설정 하면 [UnrestrictedViewer](../management/access-control/role-based-authorization.md) 데이터베이스 수준 역할에 포함 되지 않은 모든 보안 주체 (테이블/데이터베이스/클러스터 관리자)가 테이블의 데이터를 쿼리할 수 없습니다.

[UnrestrictedViewer](../management/access-control/role-based-authorization.md) 역할은 사용 하도록 설정 된 데이터베이스의 *모든* 테이블에 대 한 보기 권한을 부여 합니다 .이는 현재 보안 주체가 데이터베이스를 쿼리할 수 있는 권한이 이미 부여 된 것으로 가정 합니다 (데이터베이스 관리자/사용자/뷰어). [Databaseadmin](../management/access-control/role-based-authorization.md)에서 역할에 보안 주체를 추가 하거나 제거 하는 작업을 수행할 수 있습니다.

> [!NOTE]
> [행 수준 보안 정책을](./rowlevelsecuritypolicy.md) 사용 하는 테이블에는 RestrictedViewAccess 정책을 구성할 수 없습니다.

RestrictedViewAccess 정책을 관리 하는 제어 명령에 대 한 자세한 [내용은 여기를 참조](../management/restrictedviewaccess-policy.md)하세요.