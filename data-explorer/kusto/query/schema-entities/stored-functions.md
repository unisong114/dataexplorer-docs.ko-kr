---
title: 저장된 함수 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 저장된 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2b26fc443558724f84e76d13bf9e255a65838a2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509353"
---
# <a name="stored-functions"></a>저장 함수

**함수**는 다시 사용할 수 있는 쿼리 또는 쿼리 부분입니다. 함수는 저장된 함수라는 데이터베이스 엔터티(예: 테이블과 유사)로 저장할 수 **있습니다.** **함수는 람다 식이라는**메커니즘을 통해 [let 문을](../letstatement.md) 사용하여 쿼리 내부의 임시 방식으로 만들 수도 있습니다.

함수 호출 구문 및 규칙에 대한 자세한 내용은 [사용자 정의 함수를](../functions/user-defined-functions.md) 참조하십시오.
저장소 함수를 만들고 관리하는 방법에 대한 자세한 내용은 [관리 기능에서](../../management/functions.md)찾을 수 있습니다.