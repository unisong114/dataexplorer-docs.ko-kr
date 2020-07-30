---
title: current_cluster_endpoint ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 current_cluster_endpoint ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348720"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

현재 쿼리 중인 클러스터의 네트워크 끝점 (DNS 이름)을 반환 합니다.

## <a name="syntax"></a>Syntax

`current_cluster_endpoint()`

## <a name="returns"></a>반환

쿼리 중인 현재 클러스터의 네트워크 끝점 (DNS 이름)을 형식의 값으로 입력 `string` 합니다.

## <a name="example"></a>예제

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```