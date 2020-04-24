---
title: 데이터베이스 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509438"
---
# <a name="databases"></a>데이터베이스

Kusto는 상위 수준 엔터티가 있는 데이터를 저장하는 `database`관계 모델을 따릅니다. 

Kusto 클러스터는 여러 데이터베이스를 호스트할 수 있으며, 여기서 각 데이터베이스는 [테이블,](tables.md)저장된 함수 및 [외부 테이블의](externaltables.md)자체 컬렉션을 [호스트합니다.](stored-functions.md)
각 데이터베이스에는 [RBAC(역할 기반 액세스 제어) 모델을](../../management/access-control/index.md) 기반으로 하는 자체 사용 권한 집합이 있습니다.

**참고 사항**  

* 데이터베이스 이름은 대/소문자를 구분하지 않습니다.
* 데이터베이스 이름은 [엔터티 이름에](./entity-names.md)대한 규칙을 따라야 합니다.
* 클러스터당 데이터베이스의 최대 제한은 10,000개입니다.