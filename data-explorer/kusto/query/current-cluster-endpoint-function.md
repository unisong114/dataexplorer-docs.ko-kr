---
title: current_cluster_endpoint() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 current_cluster_endpoint()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516850"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

쿼리중인 현재 클러스터의 네트워크 끝점(DNS 이름)을 반환합니다.

**구문**

`current_cluster_endpoint()`

**반환**

형식의 `string`값으로 쿼리중인 현재 클러스터의 네트워크 끝점(DNS 이름)입니다.

**예제**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```