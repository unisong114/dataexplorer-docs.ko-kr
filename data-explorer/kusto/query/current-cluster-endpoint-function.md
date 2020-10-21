---
title: current_cluster_endpoint ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 current_cluster_endpoint ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252558"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

현재 쿼리 중인 클러스터의 네트워크 끝점 (DNS 이름)을 반환 합니다.

## <a name="syntax"></a>구문

`current_cluster_endpoint()`

## <a name="returns"></a>반환

쿼리 중인 현재 클러스터의 네트워크 끝점 (DNS 이름)을 형식의 값으로 입력 `string` 합니다.

## <a name="example"></a>예제

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```