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
ms.openlocfilehash: 9ba9f8b0f0a940357eab2277eb24e18b92718bc4
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85901812"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess 정책

*RestrictedViewAccess* 는 데이터베이스의 테이블에 대해 사용 하도록 설정할 수 있는 선택적 정책입니다.

테이블에 대해이 정책을 사용 하는 경우 테이블의 데이터는 데이터베이스에서 [UnrestrictedViewer](../management/access-control/role-based-authorization.md) 역할을 가진 보안 주체로만 쿼리할 수 있습니다.
[UnrestrictedViewer](../management/access-control/role-based-authorization.md) 데이터베이스 수준 역할에 등록 되지 않은 모든 보안 주체는 테이블의 데이터를 쿼리할 수 없습니다. 등록 되지 않은 테이블/데이터베이스/클러스터 관리자도 마찬가지입니다.

[UnrestrictedViewer](../management/access-control/role-based-authorization.md) 역할은 정책을 사용 하도록 설정 된 데이터베이스의 *모든* 테이블에 대 한 보기 권한을 부여 합니다.
데이터베이스 관리자/사용자/뷰어가 현재 주 서버에 이미 데이터베이스를 쿼리할 수 있는 권한이 부여 되었습니다. 보안 주체를 추가 하거나 제거 하는 작업은 [Databaseadmin](../management/access-control/role-based-authorization.md)에서 수행할 수 있습니다.

> [!NOTE]
> [행 수준 보안 정책을](./rowlevelsecuritypolicy.md) 사용 하는 테이블에는 RestrictedViewAccess 정책을 구성할 수 없습니다.

자세한 내용은 [RestrictedViewAccess 정책을](../management/restrictedviewaccess-policy.md)관리 하는 제어 명령을 참조 하세요.
