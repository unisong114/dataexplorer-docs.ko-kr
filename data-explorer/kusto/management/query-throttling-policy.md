---
title: 쿼리 제한 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure의 쿼리 제한 정책에 대해 설명 데이터 탐색기
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570568"
---
# <a name="query-throttling-policy"></a>쿼리 제한 정책

클러스터가 동시에 실행할 수 있는 동시 쿼리 수를 제한 하는 쿼리 제한 정책을 정의 합니다. 이 정책은 클러스터가 유지할 수 있는 것 보다 더 많은 동시 쿼리로 오버 로드 되지 않도록 보호 합니다. 정책은 런타임에 변경 될 수 있으며, alter policy 명령이 완료 된 직후에 발생 합니다.

* [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 조정 정책을 표시 합니다.
* [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 조정 정책을 설정 합니다.
* [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 제한 정책을 삭제 합니다.

> [!NOTE]
> 철저 한 테스트 없이 쿼리 제한 정책을 변경 하지 마십시오. 클러스터 리소스가 부하에 맞게 크기 조정 되지 않은 경우에는 변경으로 인해 성능이 저하 될 수 있으므로 쿼리가 실패할 수 있습니다. 
