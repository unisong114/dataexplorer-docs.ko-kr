---
title: current_database() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 current_database()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516748"
---
# <a name="current_database"></a>current_database()

범위에서 데이터베이스 이름을 반환합니다(다른 데이터베이스를 지정하지 않은 경우 모든 쿼리 엔터티가 확인되는 데이터베이스).

**구문**

`current_database()`

**반환**

형식의 `string`값으로 범위에 있는 데이터베이스의 이름입니다.

**예제**

```kusto
print strcat("Database in scope: ", current_database())
```