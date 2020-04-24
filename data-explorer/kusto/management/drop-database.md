---
title: .drop 데이터베이스 예쁜 이름 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .drop 데이터베이스 예쁜 이름에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521185"
---
# <a name="drop-database-prettyname"></a>.drop 데이터베이스 예쁜 이름

데이터베이스의 예쁜 (친숙한) 이름을 삭제합니다.
[데이터베이스관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.drop``database` *데이터베이스 이름*`prettyname`

**출력 반환**
 
|출력 매개 변수 |Type |Description 
|---|---|---
|DatabaseName |String |데이터베이스 이름입니다.
|예쁜 이름 |String |데이터베이스 꽤 이름 (드롭 작업 후 null)