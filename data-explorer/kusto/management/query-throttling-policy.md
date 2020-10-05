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
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734028"
---
# <a name="query-throttling-policy"></a>쿼리 제한 정책

클러스터에서 동시에 실행 하는 쿼리 크기를 제한 하는 쿼리 제한 정책을 정의 합니다. 정책은 런타임에 변경 될 수 있으며 alter policy 명령이 완료 된 직후에 발생 합니다.

* [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 조정 정책을 표시 합니다.
* [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 조정 정책을 설정 합니다.
* [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling)를 사용 하 여 클러스터의 현재 쿼리 제한 정책을 삭제 합니다.

> [!NOTE]
> 쿼리 제한 정책을 정의 하지 않으면 동시 쿼리 수가 많으면 클러스터 액세스할 수 없게 또는 성능 저하가 발생할 수 있습니다.
